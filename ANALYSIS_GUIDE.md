# Analysis Guide - Colonist.io Game Data

This guide shows how to analyze the game data using Python.

## Setup

```python
import json
from pathlib import Path
from collections import defaultdict

data_dir = Path("games")
game_files = list(data_dir.glob("*.json"))
print(f"Found {len(game_files)} games")
```

## Example 1: Basic Win Rate Analysis

```python
def analyze_basic_stats():
    """Calculate basic statistics."""
    stats = {
        'total_games': 0,
        'total_turns': 0,
        'position_wins': {1: 0, 2: 0, 3: 0, 4: 0}
    }

    for game_file in game_files:
        with open(game_file) as f:
            game = json.load(f)

        data = game['data']
        play_order = data.get('playOrder', [])
        end_game = data['eventHistory'].get('endGameState', {})

        if len(play_order) != 4:
            continue

        # Find winner
        winner_color = None
        for color_str, pdata in end_game.get('players', {}).items():
            if pdata.get('winningPlayer'):
                winner_color = int(color_str)
                break

        if winner_color is None:
            continue

        stats['total_games'] += 1
        stats['total_turns'] += end_game.get('totalTurnCount', 0)

        # Track winner position
        for pos, color in enumerate(play_order, 1):
            if color == winner_color:
                stats['position_wins'][pos] += 1

    # Print results
    print(f"Total games: {stats['total_games']}")
    print(f"Avg turns per game: {stats['total_turns'] / stats['total_games']:.1f}")
    print("\nWin rate by position:")
    for pos in [1, 2, 3, 4]:
        wr = stats['position_wins'][pos] / stats['total_games'] * 100
        print(f"  Position {pos}: {wr:.2f}%")

analyze_basic_stats()
```

## Example 2: Development Card Analysis

```python
CARD_ENUMS = {10: 'Knight', 11: 'Knight', 12: 'Monopoly',
              13: 'VP', 14: 'Road Building', 15: 'Year of Plenty'}

def analyze_dev_cards():
    """Analyze development card usage and win rates."""
    card_stats = defaultdict(lambda: {'played': 0, 'wins': 0})

    for game_file in game_files:
        with open(game_file) as f:
            game = json.load(f)

        data = game['data']
        events = data['eventHistory'].get('events', [])
        end_game = data['eventHistory'].get('endGameState', {})
        play_order = data.get('playOrder', [])

        if len(play_order) != 4:
            continue

        # Find winner
        winner_color = None
        for color_str, pdata in end_game.get('players', {}).items():
            if pdata.get('winningPlayer'):
                winner_color = int(color_str)
                break

        # Track cards played per player
        player_cards = defaultdict(list)

        for event in events:
            gls = event.get('stateChange', {}).get('gameLogState', {})
            for log in gls.values():
                text = log.get('text', {})
                if text.get('type') == 20:  # Dev card played
                    player = text.get('playerColor')
                    card = CARD_ENUMS.get(text.get('cardEnum'))
                    if player and card:
                        player_cards[player].append(card)

        # Update stats
        for color in play_order:
            is_winner = (color == winner_color)
            for card in player_cards[color]:
                card_stats[card]['played'] += 1
                if is_winner:
                    card_stats[card]['wins'] += 1

    # Print results
    print("Dev Card Win Rates:")
    for card, stats in sorted(card_stats.items()):
        if stats['played'] > 0:
            wr = stats['wins'] / stats['played'] * 100
            print(f"  {card}: {stats['played']:,} played, {wr:.1f}% win rate")

analyze_dev_cards()
```

## Example 3: Analyzing Game Events

```python
def parse_game_events(game_file):
    """Parse all events from a single game."""
    with open(game_file) as f:
        game = json.load(f)

    data = game['data']
    events = data['eventHistory']['events']

    current_turn = 0
    parsed_events = []

    for event in events:
        sc = event.get('stateChange', {})
        cs = sc.get('currentState', {})

        # Track turn number
        if 'completedTurns' in cs:
            current_turn = cs['completedTurns']

        # Parse log entries
        gls = sc.get('gameLogState', {})
        for log_id, log in gls.items():
            text = log.get('text', {})
            log_type = text.get('type')
            player = text.get('playerColor')

            parsed_events.append({
                'turn': current_turn,
                'type': log_type,
                'player': player,
                'data': text
            })

    return parsed_events

# Example usage
events = parse_game_events(game_files[0])
for e in events[:10]:
    print(e)
```

## Example 4: Board State Analysis

```python
def get_initial_settlements(game_file):
    """Extract initial settlement positions for all players."""
    with open(game_file) as f:
        game = json.load(f)

    data = game['data']
    events = data['eventHistory']['events']

    settlements = defaultdict(list)

    # First 8 events typically contain initial placements
    for event in events[:20]:
        sc = event.get('stateChange', {})
        corners = sc.get('mapState', {}).get('tileCornerStates', {})

        for corner_id, corner_info in corners.items():
            owner = corner_info.get('owner')
            building_type = corner_info.get('buildingType')

            if owner and building_type == 1:  # Settlement
                settlements[owner].append(corner_id)

    return dict(settlements)

# Example
settlements = get_initial_settlements(game_files[0])
print("Initial settlements by player:")
for player, corners in settlements.items():
    print(f"  Player {player}: corners {corners}")
```

## Example 5: Trade Analysis

```python
RESOURCES = {1: 'Brick', 2: 'Wool', 3: 'Grain', 4: 'Ore', 5: 'Lumber'}

def analyze_trades():
    """Analyze trading patterns."""
    bank_trades = defaultdict(int)  # By ratio (2:1, 3:1, 4:1)
    resource_received = defaultdict(int)

    for game_file in game_files[:1000]:  # Sample
        with open(game_file) as f:
            game = json.load(f)

        events = game['data']['eventHistory']['events']

        for event in events:
            gls = event.get('stateChange', {}).get('gameLogState', {})
            for log in gls.values():
                text = log.get('text', {})

                # Bank trade (type 14)
                if text.get('type') == 14:
                    given = text.get('offeredCards', [])
                    received = text.get('wantedCards', [])
                    if given and received:
                        ratio = f"{len(given)}:{len(received)}"
                        bank_trades[ratio] += 1
                        for card in received:
                            resource_received[RESOURCES.get(card, 'Unknown')] += 1

    print("Bank trade ratios:")
    for ratio, count in sorted(bank_trades.items()):
        print(f"  {ratio}: {count:,}")

    print("\nResources received from bank:")
    for res, count in sorted(resource_received.items(), key=lambda x: -x[1]):
        print(f"  {res}: {count:,}")

analyze_trades()
```

## Example 6: Timing Analysis

```python
def analyze_timing(event_type, card_enum=None):
    """Analyze when certain events happen in games."""
    timing_buckets = defaultdict(lambda: {'count': 0, 'wins': 0})

    for game_file in game_files:
        with open(game_file) as f:
            game = json.load(f)

        data = game['data']
        events = data['eventHistory']['events']
        end_game = data['eventHistory'].get('endGameState', {})
        total_turns = end_game.get('totalTurnCount', 1)

        # Find winner
        winner_color = None
        for color_str, pdata in end_game.get('players', {}).items():
            if pdata.get('winningPlayer'):
                winner_color = int(color_str)
                break

        current_turn = 0
        for event in events:
            sc = event.get('stateChange', {})
            if 'completedTurns' in sc.get('currentState', {}):
                current_turn = sc['currentState']['completedTurns']

            gls = sc.get('gameLogState', {})
            for log in gls.values():
                text = log.get('text', {})
                if text.get('type') == event_type:
                    if card_enum and text.get('cardEnum') != card_enum:
                        continue

                    player = text.get('playerColor')
                    pct = (current_turn / total_turns) * 100
                    bucket = int(pct // 10) * 10
                    bucket = min(bucket, 90)

                    timing_buckets[bucket]['count'] += 1
                    if player == winner_color:
                        timing_buckets[bucket]['wins'] += 1

    print("Timing analysis:")
    for bucket in range(0, 100, 10):
        stats = timing_buckets[bucket]
        if stats['count'] > 0:
            wr = stats['wins'] / stats['count'] * 100
            print(f"  {bucket}-{bucket+10}%: {stats['count']:,} events, {wr:.1f}% win rate")

# Example: Knight timing
analyze_timing(event_type=20, card_enum=11)
```

## Key Analysis Questions

Here are some interesting questions you can explore:

1. **Opening Strategy**: Which initial settlement positions lead to higher win rates?
2. **Resource Balance**: What's the optimal balance of different resources?
3. **Development Cards**: When is the best time to play each card type?
4. **Trading**: Do winners trade more or less? What resources?
5. **Achievements**: How important is Largest Army vs Longest Road?
6. **Game Length**: Do longer games favor certain strategies?

## Tips

- Use `defaultdict` for counting to avoid key errors
- Sample games first (`game_files[:1000]`) when testing code
- Track `completedTurns` from `currentState` to get timing
- Winner is in `endGameState.players[color].winningPlayer`
- Player colors are integers (1-5), not indices (0-3)
