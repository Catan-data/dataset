# Key Findings from Colonist.io Data Analysis

Analysis based on **41,310 games** and **165,240 player instances**.

---

## 1. Turn Order is Balanced

| Pick Position | Win Rate |
|---------------|----------|
| 1st pick | 24.91% |
| 2nd pick | 24.95% |
| 3rd pick | 25.18% |
| 4th pick | 24.95% |

**Conclusion**: The snake draft system (1-2-3-4-4-3-2-1) perfectly balances first-player advantage.

---

## 2. The Knight Trap

| Knights Played | Win Rate | Note |
|----------------|----------|------|
| 0 | 18.2% | No dev cards |
| 1 | 18.8% | TRAP |
| 2 | 16.7% | TRAP |
| 3 | 42.3% | Largest Army! |
| 3 + LA | **52.4%** | Best |
| 4+ | 35-37% | Diminishing returns |

**Key Insight**: Playing 1-2 knights is WORSE than playing 0. You need 3+ to get Largest Army!

---

## 3. Monopoly is Powerful

| Monopoly Cards | Win Rate |
|----------------|----------|
| 0 | 20.0% |
| 1 | **41.2%** |
| 2 | **54.9%** |

### Best Monopoly Timing (10% buckets)
| Game Progress | Win Rate |
|---------------|----------|
| 0-30% | 30-34% |
| 50-70% | 34-37% |
| 90-100% | **57.1%** |

### Best Resource to Monopolize
| Resource | Early Game | Late Game |
|----------|------------|-----------|
| Ore | 38% | 46% |
| Lumber | 34-40% | 47% |
| Brick | 30-31% | 49% |
| Grain | 24-32% | 46% |
| Wool | 29-32% | 48% |

---

## 4. Road Building: Play Early!

| Timing | Win Rate |
|--------|----------|
| 10-20% | **37.9%** |
| 20-40% | 31% |
| 80-90% | 28.3% |

### Road Building + Longest Road
| Combination | Win Rate |
|-------------|----------|
| 1 Road + LR | **57.9%** |
| 2+ Roads + LR | **62.9%** |

---

## 5. Year of Plenty: Midgame is Best

Consistent ~30% win rate across most timings, best used to accelerate towards milestones.

---

## 6. VP Cards are Valuable

| VP Cards | Win Rate |
|----------|----------|
| 0 | 20.0% |
| 1 | **41.3%** |
| 2 | **56.0%** |

---

## 7. Best Dev Card Combinations

| Combination | Win Rate |
|-------------|----------|
| K3+ + M1 + R1 + VP1 + Y1 | **50.2%** |
| K3+ + M1 + R1 + VP1 | **49.5%** |
| K3+ + M1 + VP1 | **47.7%** |

(K=Knight, M=Monopoly, R=Road Building, Y=Year of Plenty, VP=Victory Point)

---

## 8. Pip Values: Diversity > Total

**CORRECTED Analysis** (previous claims of "6+8 = 39.6%" were WRONG):

| Pip Combination | Win Rate |
|-----------------|----------|
| Both 6 and 8 | 24.56% |
| Neither 6 nor 8 | 25.27% |
| Very high pips (22+) | **22.57%** (WORSE!) |

**Key Insight**: High total pip values often mean overlapping dice numbers, which is BAD:
- Less resource diversity
- More vulnerable to robber
- "All eggs in one basket"

---

## 9. Trading Patterns

### Trade Efficiency Paradox
| Efficiency (received/given) | Win Rate |
|-----------------------------|----------|
| 0.0-0.3 (very low) | 17.5% |
| 0.3-0.5 (moderate) | **28.2%** |
| 0.5-0.7 (high) | 24.5% |
| 0.9-1.1 (even) | 8.0% |

**Key Insight**: Winners "lose" on individual trades but win the game. Making "bad" trades for the right resources is good strategy!

### Resource Flow
- **Most wanted**: Ore (+129k net), Grain (+95k net)
- **Most traded away**: Lumber (-126k), Wool (-125k)

### Bank Trade Efficiency
| Trade Type | Winner vs Loser Ratio |
|------------|----------------------|
| 2:1 port | 1.40x more by winners |
| 3:1 port | 1.33x more by winners |
| 4:1 (no port) | 0.89x (losers use more) |

---

## 10. Strategic Recommendations

1. **Knights**: Play 3+ or none. 1-2 is a trap!
2. **Monopoly**: Save for late game (50%+) for maximum impact
3. **Road Building**: Use early (10-30%) for territorial control
4. **Year of Plenty**: Flexible - use when you need a push
5. **Ports matter**: 2:1 and 3:1 ports give significant advantage
6. **Diversity > Pips**: Spread your settlements, don't cluster on 6-8
7. **Trade aggressively**: "Losing" trades for Ore/Grain is winning strategy

---

*Analysis performed January 2025*
