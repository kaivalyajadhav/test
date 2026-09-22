All 137 tests pass. The `src/basket.py` implementation is complete per spec §2.1 and §5.1:

**Implemented features:**

1. **ATM 3-strike basket:**
   - K0 = strike nearest parity forward F (NOT spot)
   - Neighbors by POSITION in listed strike array (K0±1) — never fixed-point offset
   - Average call IV and put IV at each strike (skip rejected leg)
   - Weights [0.25, 0.50, 0.25] renormalized if strike missing
   - Validity gate: `min_quotes_atm` surviving quotes
   - Returns `iv_atm`, `atm_strike`, `listed_strikes`, `n_used`, `n_rejected`, `rejection_reasons`, `strike_ivs`, `weights_used`, `k_minus`, `k0`, `k_plus`

2. **Delta-band (30-40 delta) basket:**
   - Uses SELF-COMPUTED delta from `iv_engine` (vendor Greeks forbidden)
   - Put side: `|put delta|` in `delta_band`, Call side: `call delta` in band
   - Average self-computed IVs per side, then average two sides
   - Fallback: no strike in band → nearest to `delta_fallback_target`, logs fallback rate
   - Either side unavailable → `iv_delta = None` (doesn't fail cycle; `iv_atm` still produced)

3. **STRIKE GRID SAFETY (spec §5.1):**
   - `snap_strike(grid=5.0)` is FORBIDDEN — does not exist in codebase
   - NEVER computes strike arithmetically
   - `verify_strike_listed()` raises `StrikeGridError` (STRIKE_NOT_LISTED) if required strike not in array
   - NEVER substitutes a neighbor

4. **No vendor branching** — pure normalized quote processing

5. **Tests validate:**
   - Positional neighbor selection across 5/10/25-point spacing boundaries
   - STRIKE_NOT_LISTED raises rather than substituting
   - ATM basket weights renormalization
   - Delta-band fallback behavior
   - Put delta uses absolute value, call delta uses positive value
