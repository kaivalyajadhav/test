Done. Here's a summary of what was built:

---

## Summary

### Created Files

| File | Purpose |
|------|---------|
| `nfh/core/engine.py` | Core cycle engine with exact evaluation order |
| `nfh/adapters/backtest.py` | Backtest adapter with snapshot latency, fill modes, commissions |
| `tests/test_engine.py` | Comprehensive test suite (19 tests) |

### Engine Features (`nfh/core/engine.py`)

**Exact Cycle Order (AGENTS.md §3.5):**
1. `risk_halts()` - VIX, rapid moves, daily P&L limits
2. `breakeven_close()` - Exit at 85% of credit distance
3. `stop_loss()` - Exit at -25% of credit
4. `drop_check()` - **MUST precede adds** - touch confirmation (10s)
5. `add_check()` - Tier-based adds, wing pricing to $0.40 target
6. `eod_handling()` - Settlement at index EOD close

**Key Correctness Guarantees:**
- Different clocks: 1s SPX for triggers, 1m options for pricing
- Pure functions from `rules.py`/`sizing.py` only
- Adapter interface - engine doesn't know backtest vs live
- Structured event log with all required types (ENTRY, ADD, DROP, CLOSE_BREAKEVEN, CLOSE_STOP, SETTLEMENT, etc.)

### Backtest Adapter (`nfh/adapters/backtest.py`)

**Snapshot Latency (Primary Feature):**
- `next_snapshot` (default): 14:32:07 trigger → 14:33:00 option snapshot (realistic)
- `same_snapshot`: 14:32:07 trigger → 14:32:00 snapshot (optimistic bound)

**Fill Modes:**
- `optimistic`: mid price
- `realistic` (default): mid + $0.10 against per fly per side
- `pessimistic`: full bid/ask

**Other Features:**
- No-fill when combo spread > $1.50 → logs `ENTRY_REJECTED_SPREAD`
- Settlement at index EOD close (not 16:00 quote) — captures $14/contract difference
- Commissions ($0.65/leg) + exchange fees ($0.02/contract)
- `touch_confirm_seconds` enforced against 1s data

### Tests (19 pass)

| Test | Verifies |
|------|----------|
| `test_evaluation_order_drop_before_add` | DROP precedes ADD in event log |
| `test_snapshot_latency_next_snapshot` | 14:32:07 → 14:33:00 |
| `test_snapshot_latency_same_snapshot` | 14:32:07 → 14:32:00 |
| `test_snapshot_latency_edge_cases` | Boundary conditions |
| `test_settlement_uses_eod_close` | Settlement at index close |
| `test_settlement_captures_14_cent_difference` | $14/contract difference |
| `test_touch_confirm_6_seconds_no_fire` | 6s < 10s threshold |
| `test_touch_confirm_11_seconds_fires` | 11s > 10s threshold |
| `test_touch_confirm_resets_on_no_touch` | Tracking resets |
| `test_event_structure_complete` | All 6 required event types generated |

**Total test suite: 172 pass, 1 fails by design** (parquet purity test correctly detects 18 violations in root scripts).
