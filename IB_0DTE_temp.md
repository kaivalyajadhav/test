

### Built `core/sizing.py` — 13 pure functions per AGENTS.md §5

| Function | Purpose |
|----------|---------|
| `equity_required(n, cfg)` | `n × target_flies × reserve / bp_util` |
| `flies_by_bp(equity, contracts, bp_in_use, cfg)` | Available flies from BP budget |
| `weekly_contract_update(current, weekly_pnl, equity, cfg)` | +1 win, -1 loss, 0 flat, floor 1, **BP constraint** |
| `strategy_cap(now)` | 2 pre-14:00, 10 post-14:00 |
| `effective_fly_cap(now, equity, contracts, bp_in_use, cfg)` | `min(strategy_cap, flies_by_bp, 10)` |
| `pdt_blocked(equity, cfg)` | Threshold 25,000 |
| `bp_blocked_add_today(days, cfg)` | >2 days forces `contracts -= 1` next week |
| `next_week_contracts(current, weekly_pnl, equity, bp_days, cfg)` | Weekly update + BP-blocked forced reduction |
| `apply_sizing_mode(mode, ...)` | Dispatch: `fixed_1`, `weekly_scaling`, `equity_banded`, `unconstrained` |
| `reserve_at_entry(contracts, wing_width, credit, cfg)` | `(wing - credit) × 100 × contracts` |
| `release_on_exit(reserve)` | Returns reserve amount |

### All 133 tests pass (42 new + 46 rules + 19 loader + 26 fly)

**AGENTS.md §8 mandatory cases verified:**
- ✅ Win +1, loss -1, floor 1
- ✅ Step-up to 2 blocked below $50k at 5-fly target
- ✅ 25k/1 contract → 5 flies; 25k/2 contracts → 2 flies
- ✅ Drop releases reserve → capacity increases
- ✅ BP_BLOCKED_ADD 3 days forces `contracts -= 1` next week
- ✅ Equity $25k → $50k raises cap automatically
- ✅ PDT blocked at 24,999; not at 25,000
- ✅ **Below $50k: `fixed_1` == `weekly_scaling`** (26-week simulation identical)

All functions are pure: no I/O, no `datetime.now()`, no globals, time always explicit parameter.
