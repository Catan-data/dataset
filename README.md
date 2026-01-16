#  Game Data - Public Dataset

This dataset contains **43,947 games** (online Catan), anonymized for research and analysis purposes.

## Download

**[Download games.tar.gz from Releases](https://github.com/Catan-data/dataset/releases/latest)**

```bash
# Extract the data
tar -xzf games.tar.gz

# This creates a 'games/' folder with 43,947 JSON files (~6.9GB uncompressed)
```

## Dataset Overview

- **Total Games**: 43,947 four-player games
- **Total Players**: ~175,000 player instances
- **Game Mode**: Standard 4-player Catan
- **Data Format**: JSON files (one per game)

## Data Structure

Each game file contains:

```json
{
  "data": {
    "playerUserStates": [...],      // Player info (anonymized)
    "playOrder": [2, 5, 3, 1],      // Turn order by player color
    "gameSettings": {...},           // Game configuration
    "eventHistory": {
      "events": [...],               // All game events
      "endGameState": {...},         // Final game state with winner
      "totalTurnCount": 69,          // Game length
      "initialState": {...}          // Starting board state
    }
  }
}
```

## Key Data Fields

### Player Colors
Players are identified by color numbers:
- 1 = Blue
- 2 = Red
- 3 = Orange
- 4 = Brown (rare)
- 5 = White

### Event Types (in `gameLogState`)
Events are stored in `stateChange.gameLogState` with `text.type`:

| Type | Description |
|------|-------------|
| 4 | Building placed (settlement/road/city) |
| 11 | Resource received from dice roll |
| 14 | Trade with bank |
| 15 | Trade with player |
| 20 | Development card played |
| 22 | Development card bought |
| 45 | Game winner declared |
| 49 | Robber moved |
| 55 | Cards discarded (7 rolled) |
| 86 | Monopoly result |

### Development Card Enums (`cardEnum` in type 20)
| Enum | Card |
|------|------|
| 10, 11 | Knight |
| 12 | Monopoly |
| 13 | Victory Point |
| 14 | Road Building |
| 15 | Year of Plenty |

### Resource Enums
| Enum | Resource |
|------|----------|
| 1 | Brick |
| 2 | Wool |
| 3 | Grain |
| 4 | Ore |
| 5 | Lumber |

### Board Coordinates
- **Hexes**: `(x, y)` coordinates
- **Corners**: `(x, y, z)` where z=0 is "up" corner, z=1 is "down" corner
- **Edges**: Edge IDs in `tileEdgeStates`

### End Game State
Winner information is in `data.eventHistory.endGameState.players`:

```json
{
  "players": {
    "2": {
      "rank": 1,
      "color": 2,
      "winningPlayer": true,
      "victoryPoints": {
        "0": 3,  // Settlements
        "1": 2,  // Cities
        "2": 1,  // Largest Army (2 VP)
        "3": 1,  // Longest Road (2 VP)
        "4": 1   // VP cards
      }
    }
  }
}
```

## Pip Values (Dice Probability)
Standard Catan pip values:
| Number | Pips |
|--------|------|
| 2, 12 | 1 |
| 3, 11 | 2 |
| 4, 10 | 3 |
| 5, 9 | 4 |
| 6, 8 | 5 |
| 7 | - (robber) |

## Privacy Notice

This dataset has been **anonymized**:
- ❌ No usernames
- ❌ No user IDs
- ❌ No country codes
- ✅ Player colors preserved
- ✅ All game events preserved
- ✅ Board states preserved

## License

This dataset is provided for **research and educational purposes only**.

## Citation

If you use this dataset, please cite:
```
MR. MUCHO BUCHO Game Data (2025)
43,947 anonymized 4-player Catan games
https://github.com/Catan-data/dataset
```
