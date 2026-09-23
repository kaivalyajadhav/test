Done.

### Built `core/integrity.py` — Combo-fill integrity per AGENTS.md §3.9

| Function | Purpose |
|----------|---------|
| `verify_fill(order, fill_report)` | Checks all 4 legs: present, 1:1:1:1 ratio, correct sides, correct strikes/rights |
| `enforce_fill_integrity(order, fill_report)` | Main entry; **raises `FILL_INTEGRITY_BREACH` on ANY mismatch** — never silent |
| `remediate(breach, adapter)` | Tries market completion → if fails, flattens → if both fail, **raises `RuntimeError`** (never returns unbalanced state) |
| `ComboOrder` / `FillReport` | Validated dataclasses (4 legs, 1:1:1:1, correct sides per role, unique roles) |
| `assert_integrity_checking_enabled()` | Startup assertion: **no config can disable integrity** |

### All 152 tests pass (19 new + 133 existing)

**AGENTS.md §8 mandatory cases verified:**
- ✅ Clean 4-of-4 fill passes
- ✅ 3-of-4 fill raises `FILL_INTEGRITY_BREACH`
- ✅ 4-leg wrong ratios raises `FILL_INTEGRITY_BREACH`
- ✅ After remediation fails, no unbalanced position persists
- ✅ **No config key can disable integrity checking**
- ✅ ComboOrder validation rejects wrong leg count, duplicate roles, wrong sides, wrong ratios

### Core layer complete
| Module | Functions | Tests |
|--------|-----------|-------|
| `core/rules.py` | 8 pure rules | 46 |
| `core/sizing.py` | 13 pure sizing | 42 |
| `core/integrity.py` | 3 integrity + remediation | 19 |

**All 152 tests pass. All functions pure: no I/O, no `datetime.now()`, no globals, time always explicit parameter.**
