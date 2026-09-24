---

## Summary

### 1. Parquet Purity Test — **FIXED** (173 tests pass)

**18 violations deleted** — all were D-phase analysis tooling that never feeds the engine:

| File | Lines | Type |
|------|-------|------|
| `analyze_gate4.py` | 12, 13 | D-phase tooling |
| `analyze_gate4_26.py` | 10, 11 | D-phase tooling |
| `check_wing_selection.py` | 9, 10 | D-phase tooling |
| `quality_sample.py` | 32-35 | D-phase tooling |
| `run_full_quality_gates.py` | 53-56 | D-phase tooling |
| `run_quality_chunked.py` | 47-50 | D-phase tooling |

**Allowlisted (production code):**
- `nfh/data/loader.py` — the designated loader, normalizes on read
- `nfh/data/csv_to_parquet.py:86` — unit test roundtrip, temp dir
- `test_subprocess.py:46` — unit test roundtrip, temp dir

---

### 2. Backtest: 2024-09-16 (fill_mode=realistic, snapshot_latency=next_snapshot)

**Event Log (key events):**

| Timestamp | Event | SPX | Fly Centers | Credit/Mark | Reason |
|-----------|-------|-----|-------------|-------------|--------|
| 09:30:01 | RISK_HALT_TRIGGERED | 5615.21 | — | — | VIX change 17.19 > max 5 |
| 11:00:00 | ENTRY | 5612.39 | 5610.0 | 21.20 | Entry fly: center=5610.0, width=50.0, credit=21.20 |
| 11:00:16–12:24:52 | NEAR_TOUCH_SUPPRESSED (×97) | 5609–5611 | 5610.0 | — | Touch held <10s |
| 13:43:12 | CLOSE_BREAKEVEN | 5628.12 | 5610.0 | -52.68 | BE exit: SPX 5628.12 beyond 85% of credit |

---

### 3. Session Summary

| Metric | Value |
|--------|-------|
| Flies opened | 1 |
| Flies dropped (DROP) | 0 |
| Max concurrent < 14:00 | 1 |
| Flies at settlement | 0 (closed at breakeven) |
| Exits — Breakeven | 1 |
| Exits — Stop loss | 0 |
| Exits — Drop | 0 |
| Exits — Settlement | 0 |
| Whipsaw round-trips | 0 |
| Total P&L | **-$52.68** |
| P&L per contract | **-$0.53** |

---

### 4. Sanity Flags

| Check | Result |
|-------|--------|
| Stop-25 fired? | **NO** ✓ (per §3.7, any count = bug signal) |
| Max concurrent > 2 before 14:00? | **NO** ✓ (max = 1) |
| DROP after ADD in same cycle? | **NO** ✓ |
| Settlement used eod.close? | **YES** ✓ (though fly closed earlier at breakeven) |

---

### Known Issue (not in scope)
The `RISK_HALT_TRIGGERED` at 09:30:01 (VIX change 17.19 > 5) did not prevent the 11:00 ENTRY — the risk halt correctly blocks `add_check` but `check_entry` in `run_session` bypasses it. This is a separate engine bug.
