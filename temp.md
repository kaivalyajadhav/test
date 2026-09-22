The live path is implemented and ready. Here's the summary:

---

## LIVE PATH IMPLEMENTATION COMPLETE

### 1. Timestamp Capture (line 618 in diagnose.py)
```python
# CAPTURE TIMESTAMP ONCE per cycle - immediately before first request
capture_timestamp_utc = now_utc
capture_timestamp_et = now_et
```
This single timestamp propagates to every `QuoteInput.timestamp` via `analyze_expiration_full` → `chain_to_quote_inputs` (line 410) → `QuoteInput(timestamp=timestamp_et)` (line 250).

### 2. Implementation Status
- **Live path**: `run_live_diagnosis()` implemented in `diagnose.py:603-750`
- **Market hours check**: Enforces ≥09:45 ET (line 613)
- **Full pipeline**: Quote → Expirations → Chains → IV engine → Baskets
- **Broker verification table**: Includes straddle-approx sigma & ratio
- **Total rejection counts**: Aggregated across all expirations
- **Delta-band fallback detail**: Per-expiration with put/call candidate counts
- **Solver method tracking**: Newton/Brent/Newton→Brent counts

### 3. Cannot Run Now
```
Market not yet open (ET: 09:14:46). Must be >= 09:45 ET.
```
Current time is 09:14 ET — must wait until market hours.

### 4. Fixture Validation (confirms pipeline correctness)
| Expiration | DTE | Settlement | T_years | F | K* | iv_atm | Straddle-σ | Ratio |
|------------|-----|------------|---------|----|----|--------|------------|-------|
| 2026-10-01 | 9   | PM         | 0.0282  | 7769.25 | 7770 | 0.105866 | 0.1073 | 0.987 |
| 2026-10-06 | 14  | PM         | 0.0418  | 7772.45 | 7770 | 0.107138 | 0.1089 | 0.984 |
| 2026-10-09 | 17  | PM         | 0.0491  | 7776.80 | 7775 | 0.112208 | 0.1117 | 1.005 |

**Plausibility assertion**: No warnings triggered (all iv_atm ∈ [0.03, 0.60])

**ATM basket spacing** (asymmetric grid confirmed):
- 10/01: K-1=7760, K0=7770, K+1=7775 → 10.0 vs 5.0 pts (asymmetric)
- 10/06: K-1=7760, K0=7770, K+1=7775 → 10.0 vs 5.0 pts (asymmetric)  
- 10/09: K-1=7770, K0=7775, K+1=7780 → 5.0 vs 5.0 pts (symmetric at this DTE)

**Delta-band fallback**: 0/3 expirations (both sides have candidates)

**Quote rejections** (per expiration):
- 10/01: zero_bid:17, below_intrinsic:16, relative_spread:15
- 10/06: zero_bid:17, below_intrinsic:17, relative_spread:14
- 10/09: below_intrinsic:24, zero_bid:17, relative_spread:14

**Solver**: Newton=960, Brent=0, Newton→Brent=70 (matches fixture baseline)

---

**Ready to run** when market opens: `python -m scripts.diagnose --live`
