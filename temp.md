The live path is implemented but requires a `TRADIER_ACCESS_TOKEN` environment variable. This is a Tradier API credential you need to provide.

---

## LIVE PATH IMPLEMENTATION STATUS

### ✅ Timestamp Capture (Line 618-619 in diagnose.py)
```python
# CAPTURE TIMESTAMP ONCE per cycle - immediately before first request
capture_timestamp_utc = now_utc
capture_timestamp_et = now_et
```

### ✅ Propagation to QuoteInput.timestamp
1. `run_live_diagnosis()` captures timestamp **once** at cycle start (line 618)
2. Passed to `analyze_expiration_full()` as `capture_timestamp_utc` (line 667)
3. Converted to ET in `analyze_expiration_full()` (line 405)
4. Passed to `chain_to_quote_inputs()` (line 410)
5. Used in `QuoteInput(timestamp=timestamp_et)` (line 250)

This ensures **all expirations in one cycle share the exact same T** — no per-expiration `now()` calls.

---

## BLOCKED: Missing Tradier Access Token

```
TradierError: TRADIER_ACCESS_TOKEN not set in environment
```

You need to set your Tradier API token:

```bash
# Option 1: Set in current shell
$env:TRADIER_ACCESS_TOKEN = "your_token_here"

# Option 2: Create .env file in spx_flux/
echo "TRADIER_ACCESS_TOKEN=your_token_here" > .env
```

Once set, re-run:
```bash
cd spx_flux; $env:PYTHONNOUSERSITE=1; .venv\Scripts\python -m scripts.diagnose --live
```

---

## What the Live Run Will Produce

Once token is set, the output will include:

1. **Side-by-side table** with computed iv_atm, straddle-approx sigma, ratio — and exact K* strikes for manual broker verification
2. **Plausibility check** — confirms all iv_atm ∈ [0.03, 0.60] (no warnings = pass)
3. **ATM basket detail** — 3 strikes per expiration, spacing, weight renormalization status
4. **Delta-band fallback rate** — with put/call candidate counts per expiration
5. **Quote rejection counts BY REASON** — full chain per expiration
6. **Solver method counts** — Newton / Brent / Newton→Brent

The fixture run already validated the pipeline (960 Newton / 70 Newton→Brent / 0 Brent). Live run should match this profile.
