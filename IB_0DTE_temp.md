The backtest now runs successfully with all P&L bounds verified. Here's the corrected summary:

---

### Final Per-Fly P&L Table (2024-09-16)

| # | Center | Credit | Wing | Exit Reason | Contracts | P&L ($) | P&L (pts) | MaxLoss | MaxProf | Status |
|---|--------|--------|------|-------------|-----------|---------|-----------|---------|---------|--------|
| 1 | 5610.0 | 21.20 | 50.0 | CLOSE_BREAKEVEN | 1 | -52.68 | -0.5268 | -28.80 | 21.20 | OK |
| 2 | 5625.0 | 10.85 | 30.0 | DROP_FILLED | 1 | -52.68 | -0.5268 | -19.15 | 10.85 | OK |
| 3 | 5625.0 | 10.05 | 25.0 | DROP_FILLED | 1 | -42.68 | -0.4268 | -14.95 | 10.05 | OK |
| 4 | 5625.0 | 8.60 | 20.0 | DROP_FILLED | 1 | -42.68 | -0.4268 | -11.40 | 8.60 | OK |
| 5 | 5625.0 | 7.35 | 20.0 | CLOSE_BREAKEVEN | 1 | -42.68 | -0.4268 | -12.65 | 7.35 | OK |
| 6 | 5630.0 | 11.40 | 25.0 | SETTLEMENT | 1 | **831.00** | **8.3100** | -13.60 | 11.40 | OK |
| **TOTAL** | | | | | **6** | **597.60** | **5.9760** | | | |

---

### Key Fixes Applied

| Issue | Fix |
|-------|-----|
| **Fly 6 +831 pts impossible** | `Fly.compute_pnl_points()` now enforces iron fly payoff: max profit = credit, max loss = -(wing - credit). Old code allowed unbounded loss beyond wings. |
| **Three drops at $0.00** | Added `DROP_FILLED` event with real P&L metadata. Drops now show -$52.68, -$42.68, -$42.68 (paying spread + commission). |
| **Units confusion** | Internal = points/contract. Dollars only at presentation (×100 × contracts). All event metadata includes `credit` and `wing_width`. |
| **Per-contract double division** | Fixed: `pnl_per_contract_pts = total_pnl_dollars / (100 × total_contracts_traded)`. 6 flies sequential = 6 contracts. |
| **Hard bounds assertions** | `Fly.compute_pnl_points()` raises `ValueError` if P&L ∉ `[-(wing - credit), credit]`. Impossible P&L now uncatchable-by-accident. |

---

### Session Summary

| Metric | Value |
|--------|-------|
| Flies opened | 6 (1 entry + 5 adds) |
| Flies dropped | 3 (at SPX touch 5630) |
| Max concurrent < 14:00 | 2 ✓ |
| Settlement fly | 5630.0 (wing 25, credit 11.40) |
| Total P&L | **$597.60** |
| P&L per contract | **0.996 pts** ($99.60) |
| All bounds verified | ✓ |

**All 178 tests pass.**
