Found the bug! The close_fly method computes fill prices from the current snapshot but then uses the original leg prices (from when the fly was opened) for the close debit calculation. The fill prices are stored in the wrong Leg fields.

Let me fix this:

Bug: For short legs, fill_price goes to bid but close uses ask (original). For long legs, fill_price goes to ask but close uses bid (original).

Result: All closes use stale prices from fly open time, not current snapshot.

The drop P&Ls now vary correctly based on market conditions at each exit time:

Fly 2 (DROP 14:12): -1.2268 pts
Fly 3 (DROP 14:38): -0.1768 pts
Fly 4 (DROP 14:58): -0.4018 pts
Fly 1 (BE 13:43): +0.1232 pts (profit)
Fly 5 (BE 15:33): -0.7518 pts
Let me run the tests to ensure everything passes:

All 180 tests pass. Here's the final corrected backtest:

---

### Final Per-Fly P&L Table (2024-09-16)

| # | Center | Credit | Wing | Exit Reason | Contracts | P&L ($) | P&L (pts) | MaxLoss | MaxProf | Status |
|---|--------|--------|------|-------------|-----------|---------|-----------|---------|---------|--------|
| 1 | 5610.0 | 21.20 | 50.0 | CLOSE_BREAKEVEN | 1 | **12.32** | **0.1232** | -28.80 | 21.20 | OK |
| 2 | 5625.0 | 10.85 | 30.0 | DROP_FILLED | 1 | **-122.68** | **-1.2268** | -19.15 | 10.85 | OK |
| 3 | 5625.0 | 10.05 | 25.0 | DROP_FILLED | 1 | **-17.68** | **-0.1768** | -14.95 | 10.05 | OK |
| 4 | 5625.0 | 8.60 | 20.0 | DROP_FILLED | 1 | **-40.18** | **-0.4018** | -11.40 | 8.60 | OK |
| 5 | 5625.0 | 7.35 | 20.0 | CLOSE_BREAKEVEN | 1 | **-75.18** | **-0.7518** | -12.65 | 7.35 | OK |
| 6 | 5630.0 | 11.40 | 25.0 | SETTLEMENT | 1 | **831.00** | **8.3100** | -13.60 | 11.40 | OK |
| **TOTAL** | | | | | **6** | **587.60** | **5.8760** | | | |

---

### Session Summary

| Metric | Value |
|--------|-------|
| Flies opened | 6 (1 entry + 5 adds) |
| Flies dropped | 3 |
| Max concurrent < 14:00 | 2 ✓ |
| Flies at settlement | 1 |
| Exits — Breakeven | 2 |
| Exits — Stop loss | 0 |
| Exits — Drop | 3 |
| Exits — Settlement | 1 |
| Total P&L | **$587.60** |
| **P&L per contract (pts)** | **5.8760** ($587.60) ✓ |
| Total contracts traded | 6 |

---

### Fixes Completed

| Issue | Resolution |
|-------|------------|
| **Per-contract divided by fly count** | Fixed: `pnl_per_contract_points = sum(fly_points)` = 5.8760 pts. At position_size=1, per-contract = total points. |
| **Drop P&L identical to breakeven** | Fixed: Bug in `close_fly()` used stale original leg prices instead of current snapshot fill prices. Now each exit uses real-time bid/asks. |
| **Drop pricing verified** | All 5 non-settlement exits now have distinct P&L: -1.2268, -0.1768, -0.4018, +0.1232, -0.7518 pts |
| **Units & bounds** | Internal = points/contract. Hard assertions in `Fly.compute_pnl_points()` and `close_fly()` enforce `-(wing - credit) ≤ PnL ≤ credit`. All 6 flies pass. |

---

### Key Technical Fix

**`BacktestBroker.close_fly()` bug**: The method computed fill prices from the current snapshot but stored them in wrong Leg fields (`bid` for shorts, `ask` for longs), then used the **original** leg prices from fly creation for the close debit calculation. 

**Fix**: Compute fill prices → use directly for close debit → store in Leg. Now each close uses real-time snapshot prices.
