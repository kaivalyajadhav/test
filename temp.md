All 138 tests pass. Let me provide the summary:

---

## Corrections Applied

### 1. **diagnose.py --fixture reads ACTUAL capture timestamp from manifest**
```python
# load_capture_timestamp() - lines 64-102
# Reads CAPTURE_MANIFEST.txt → capture_start_utc (ISO 8601 UTC)
# Fails loudly if missing/unparseable:
#   FileNotFoundError: "CAPTURE_MANIFEST.txt not found... Cannot run --fixture without actual capture timestamp"
#   ValueError: "missing 'capture_start_utc' field... Cannot determine capture timestamp"
```
**Output now shows:**
```
Capture timestamp (UTC): 2026-09-21T21:35:46.701573+00:00
Capture timestamp (ET):  2026-09-21T17:35:46.701573+00:00
```
No more "noon" reconstruction — the actual 17:35 ET capture time is used.

### 2. **LIVE and BACKFILL paths — timestamp origin verification**

| Path | Where timestamp originates |
|------|---------------------------|
| **LIVE** (diagnose.py:565) | `client.get_chain()` returns raw Tradier data → **consumer must record `datetime.now(timezone.utc)` at request time** and attach to each `QuoteInput.timestamp` |
| **BACKFILL** (not yet implemented) | ThetaData provides per-row timestamps in the snapshot → **use those directly** as `QuoteInput.timestamp` |
| **ENGINE** (iv_engine.py:459) | `timestamp = chain[0].timestamp` — uses whatever the consumer passed in |

**The engine was correct** — it uses `qi.timestamp` from the `QuoteInput`. The bug was in `diagnose.py`'s fixture path reconstructing the timestamp. The live/backfill consumers must capture/propagate the actual snapshot timestamp.

### 3. **Plausibility assertion on iv_atm** (lines 104-131)
```python
def check_iv_plausibility(iv_atm, T, F, straddle_mid, expiration, dte):
    if not (0.03 <= iv_atm <= 0.60):  # SPX 5-20 DTE range
        sigma_straddle = straddle_mid / (0.8 * F * sqrt(T))
        print("!!! IV PLAUSIBILITY VIOLATION ...")
        # Shows T, F, straddle, cross-check sigma, ratio
```
**Would have caught the 8x bug instantly:** iv_atm=0.816 → WARNING with straddle-approx sigma=0.106 → ratio=7.7x

---

## Fixture Results (Corrected)

| Expiration | DTE | Capture→Expiry | T (years) | iv_atm | Straddle | Straddle-σ | Ratio |
|------------|-----|----------------|-----------|--------|----------|------------|-------|
| 2026-10-01 | 10  | 10d 20h        | 0.0283    | 0.1059 | 109.0    | 0.1088     | 0.97x |
| 2026-10-06 | 15  | 15d 20h        | 0.0418    | 0.1071 | 141.5    | 0.1092     | 0.98x |
| 2026-10-09 | 18  | 18d 20h        | 0.0491    | 0.1122 | 154.0    | 0.1117     | 1.00x |

All IVs in plausible range [0.03, 0.60] → **no warnings triggered**. Straddle cross-check matches within 2-3%.
