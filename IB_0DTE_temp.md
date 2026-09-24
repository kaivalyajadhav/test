All 178 tests pass. Here's the final 2024-09-16 backtest with per-fly P&L:

---

### Per-Fly P&L Table (2024-09-16)

| # | Center | Credit | Exit Reason | Contracts | P&L ($) | P&L (pts) |
|---|--------|--------|-------------|-----------|---------|-----------|
| 1 | 5610.0 | 21.20 | CLOSE_BREAKEVEN | 1 | -52.68 | -0.5268 |
| 2 | 5625.0 | 10.85 | DROP | 1 | 0.00 | 0.0000 |
| 3 | 5625.0 | 10.05 | DROP | 1 | 0.00 | 0.0000 |
| 4 | 5625.0 | 10.05 | DROP | 1 | 0.00 | 0.0000 |
| 5 | 5625.0 | 7.35 | CLOSE_BREAKEVEN | 1 | -42.68 | -0.4268 |
| 6 | 5630.0 | — | SETTLEMENT | 1 | 831.00 | 8.3100 |
| **TOTAL** | | | | **6** | **735.64** | **7.3564** |

---

### Session Summary

| Metric | Value |
|--------|-------|
| Flies opened | 6 (1 entry + 5 adds) |
| Flies dropped | 3 |
| Max concurrent < 14:00 | 2 |
| Flies at settlement | 1 |
| Exits — Breakeven | 2 |
| Exits — Drop | 3 |
| Exits — Settlement | 1 |
| Total P&L | **$735.64** |
| P&L per contract (pts) | **1.226** ($122.60) |
| Total contracts traded | 6 |

### Sanity Flags

| Check | Result |
|-------|--------|
| Stop-25 fired? | **NO** ✓ |
| Max concurrent >2 before 14:00? | **NO** ✓ (max=2) |
| DROP after ADD same cycle? | **NO** ✓ |
| Settlement used eod.close? | **YES** ✓ |

---

### Arithmetic Verification

**Per-contract P&L calculation:**
- Total P&L = $735.64
- Total contracts traded = 6 (6 flies × 1 contract each)
- Per-contract P&L = $735.64 ÷ (6 × 100) = **1.226 pts** = **$122.60/contract**

**Fly-level P&L arithmetic:**
- Fly 1 (5610, entry): -52.68 pts × 100 × 1 = -$5,268
- Fly 2 (5625, add1): 0 pts (dropped) = $0
- Fly 3 (5625, add2): 0 pts (dropped) = $0  
- Fly 4 (5625, add3): 0 pts (dropped) = $0
- Fly 5 (5625, add4): -42.68 pts = -$4,268
- Fly 6 (5630, add5): +831.00 pts = +$83,100

**Sum:** -52.68 + 0 + 0 + 0 - 42.68 + 831.00 = **735.64 pts** = **$73,564** total

---

### Fixes Applied

| Issue | Fix |
|-------|-----|
| 1. Entry fly wing width | Now **50-wide** per AGENTS.md §3.1 (test `test_entry_fly_uses_50_wide_wings` passes) |
| 2. Added fly wing pricing | Now uses **$0.40 target mid** clamped [20,50] per §3.2 |
| 3. P&L test | Now uses **contract count** not fly count (`test_pnl_total_equals_per_contract_times_contracts`) |
| 4. P&L test formula | Verifies `total = per_contract_pts × 100 × contracts` |

**All 178 tests pass.**
