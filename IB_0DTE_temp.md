Done.
### Built `core/rules.py` — 8 pure functions per AGENTS.md §3

| Function | Purpose |
|----------|---------|
| `select_anchor(open_flies, spx_price)` | Closest center to SPX |
| `select_tier(anchor, now)` | Low/Mid/High by credit; **15.00 & 20.00 both mid**; tight after 14:00 |
| `should_add(spx_price, open_flies, now)` | Distance > trigger → new center = `snap_strike(spx ± add_offset)` |
| `should_drop(spx_price, open_flies, touch_confirm_elapsed)` | Touch ≥10s → drop farther flies, **unconditional on P&L** |
| `should_close_breakeven(fly, spx_price, be_pct)` | Exit at `center ± 0.85×credit` → **5417.25 / 5442.75** for 5430/15 |
| `should_close_stop(fly, current_value, cfg)` | Stop at **−25% of credit** → −$375 at credit 15 |
| `entry_allowed(spx_price, chain, now)` | 11:00 ET, mid ∈ [3,50], combo spread ≤1.50 |
| `select_wing_strike(chain, center, target_mid=0.40)` | **Rejects mid < 0.20**; raises `StrikeNotListed` for unlisted strikes |

### All 91 tests pass (46 rules + 19 loader + 26 fly)

**AGENTS.md §8 mandatory cases verified:**
- ✅ be_pct exit exact values (5417.25/5442.75)
- ✅ Tier at 15.00 & 20.00 → both mid
- ✅ Two-fly cap 13:59 vs 14:01
- ✅ AM-settled root rejected
- ✅ Stop loss at credit 15 → −$375
- ✅ Stop unreachability: BE exit before stop at 5h/3h/1h/0.2h
- ✅ select_wing_strike rejects mid 0.025
- ✅ Unlisted strike raises `StrikeNotListed` (no substitution)

Also fixed `should_drop` to enforce `touch_confirm_seconds` for **exact touches**, not just near-touches.
