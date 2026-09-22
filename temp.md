All 113 tests pass (94 iv_engine + 19 tradier_client). The `src/iv_engine.py` implementation is complete with all spec §4 requirements:

**Implemented features:**
1. **Time to expiry** — PM (16:00 ET) vs AM (09:30 ET) settlement, minute-resolution calendar time, `use_trading_time=true` raises `NotImplementedError`
2. **Parity forward** — K* minimizes |C_mid - P_mid|, F = K* + exp(rT)(C-P), returns F/K*/residual
3. **Black-76 pricing & inversion** — Forward-based formulas, Brent over σ∈[0.001,5.0], Newton-Raphson with vega seed falling back to Brent, never returns unconverged
4. **Greeks from solved σ** — delta_call = exp(-rT)N(d1), delta_put = -exp(-rT)N(-d1), vega = F·exp(-rT)N'(d1)√T — **only delta permitted in project**
5. **Session window** — Rejects all timestamps before 09:45 ET
6. **Quote hygiene** — Each rule a named `QuoteRejectionReason` predicate (ZERO_BID, ZERO_ASK, CROSSED, RELATIVE_SPREAD, BELOW_INTRINSIC, ABOVE_FORWARD_BOUND, SOLVER_FAILED, IV_OUT_OF_BOUNDS, PRE_SESSION, MISSING_QUOTE); returns IV or None + rejection_reason; enforces iv_bounds
7. **No vendor branching** — Pure normalized quote processing
8. **No interpolation/forward-fill** — Missing propagates as None
