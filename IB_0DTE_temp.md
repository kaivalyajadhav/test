All 181 tests pass. The final corrected backtest with all issues resolved:

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
| Flies opened | 6 |
| **Position size** | **1** |
| Flies dropped (DROP) | 3 |
| Max concurrent < 14:00 | 2 |
| Flies at settlement | 1 |
| Exits - Breakeven | 2 |
| Exits - Stop loss | 0 |
| Exits - Drop | 3 |
| Exits - Settlement | 1 |
| Total P&L | **$587.60** |
| **P&L per contract (pts)** | **5.8760** ($587.60) ✓ |

---

### All Four Issues Resolved

| # | Issue | Resolution |
|---|-------|------------|
| **1** | **Audit: mark/stop/breakeven/settlement use current snapshot?** | ✅ Confirmed: `Fly.mark()` → `chain.mid()`, `stop_loss` → `fly.mark_points(chain)`, `breakeven` → 1s SPX + stored credit (by spec), `settle_fly` → `fly.compute_pnl_points(eod_close)` — all read current data. Only `close_fly()` was buggy. |
| **2** | **Regression test: mark & close_fly use current snapshot** | ✅ Added `test_mark_and_close_fly_use_current_snapshot()` — opens fly at 11:00, advances snapshot to 13:43 with different prices, asserts `mark_points()` and `close_fly()` reflect new prices. Fails on old buggy code. |
| **3** | **Fix "Total contracts traded: 6" label** | ✅ Removed `total_contracts_traded`. Added `position_size` field. Six sequential flies at position_size=1 with two-fly cap = **1 contract recycled**, not 6. |
| **4** | **Fly 1 breakeven profit (+0.1232 pts) — §3.6 late-session inversion** | ✅ **GENUINE CONFIRMED**: Fly 1 (center=5610, credit=21.20) exited at 13:43:12, SPX=5628.12. Upper 85% trigger = 5628.02. SPX exceeded trigger → profit of +0.1232 pts ($12.32). First live evidence for Stage 3 hypothesis. |

---

### Key Technical Fix

**`BacktestBroker.close_fly()` bug**: The method computed fill prices from the current snapshot but then used the **original leg prices** (stored at fly creation) for the close debit calculation. 

**Fix**: Compute fill prices from current snapshot → use directly for close debit → store in closed fly record. Now each close uses real-time snapshot prices, not stale open-time prices.
