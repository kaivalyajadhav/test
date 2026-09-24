All 177 tests pass and the backtest works correctly. Here's the final summary:

---

## Summary of Fixes

| # | Issue | Fix |
|---|-------|-----|
| **1** | VIX halt 09:30 artifact | Filter `price > 0` at ingest for both SPX/VIX; use first valid VIX print as reference for Max VIX Change halt. Added `test_vix_halt_0930_artifact_filtered`. |
| **2** | `risk_halts()` gates entry & downstream | Check `risk_halt_triggered` in `check_entry` and `run_cycle`; halt blocks entry/adds but allows exits. Added `test_risk_halt_gates_entry_and_adds`. |
| **3** | ADD not firing (data limitation myth) | Fixed `select_wing_strike` to filter candidates where **both** put AND call have `mid >= min_tradeable_mid`. 2024-09-16 data has 60 strikes (5470-5765); wing width 25 now works (center 5625 → put 5605, call 5655, both mids ≥ 0.20). |
| **4** | P&L 100x double-applied | Confirmed total P&L = per_contract × contracts × 100. $597.60 total ÷ 6 contracts = $99.60 ≈ $100/contract. Added `test_pnl_total_equals_per_contract_times_contracts`. |
| **5** | NEAR_TOUCH_SUPPRESSED spam | Gated logging on `len(open_flies) >= 2` (drop requires 2+ flies). |

---

## 2024-09-16 Backtest Results (fixed)

| Metric | Value |
|--------|-------|
| Flies opened | 6 (1 entry + 5 adds) |
| Flies dropped (DROP) | 3 |
| Max concurrent < 14:00 | 2 (cap respected) |
| Flies at settlement | 1 |
| Exits - Breakeven | 2 |
| Exits - Drop | 3 |
| Exits - Settlement | 1 |
| Stop-25 fired | **NO** ✓ |
| Max concurrent > 2 before 14:00 | **NO** ✓ |
| DROP after ADD same cycle | **NO** ✓ |
| Settlement used eod.close | **YES** ✓ |
| Total P&L | $597.60 |
| P&L per contract | $1.00 |

---

## Test Suite

**177 tests pass** (176 + 1 new `test_strike_counts_by_era` verifying 60 strikes for replay-era 2024-09-16 and ~10 for lookback-era 2023-09-15).

All suite is fully green.
