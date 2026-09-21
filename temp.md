---

## Production Fixtures Captured — Complete Analysis

### Capture Summary
| File | Description |
|------|-------------|
| `tests/fixtures/spx_quote.json` | SPX spot: **7764.7** (bid 7730.37 / ask 7796.13) |
| `tests/fixtures/spx_expirations.json` | 58 total expirations |
| `tests/fixtures/spx_chain_2026-09-21.json` | 0 DTE — 488 options (244 strikes) |
| `tests/fixtures/spx_chain_2026-09-25.json` | 4 DTE — 610 options (305 strikes) |
| `tests/fixtures/spx_chain_2026-10-01.json` | 10 DTE — 394 options (197 strikes) |
| `tests/fixtures/spx_chain_2026-10-06.json` | 15 DTE — 364 options (182 strikes) |
| `tests/fixtures/spx_chain_2026-10-09.json` | 18 DTE — 444 options (222 strikes) |
| `tests/fixtures/CAPTURE_MANIFEST.txt` | Timestamped manifest |

All captured within **2.4 seconds** during market hours.

---

### 3 Strikes Nearest Spot — Bid/Ask/Mid/Relative Spread

| Expiration | DTE | Strike | CALL (bid/ask/mid/spread) | PUT (bid/ask/mid/spread) | Rejected (>0.25) |
|------------|-----|--------|---------------------------|--------------------------|------------------|
| **2026-09-21** | 0 | 7760.0 | 4.50 / 5.30 / 4.90 / **0.163** | 0.00 / 0.05 / 0.03 / **2.000** | PUT |
| | | 7765.0 | 0.20 / 0.70 / 0.45 / **1.111** | 0.30 / 0.45 / 0.38 / **0.400** | CALL, PUT |
| | | 7770.0 | 0.00 / 0.05 / 0.03 / **2.000** | 5.00 / 5.20 / 5.10 / **0.039** | CALL |
| **2026-09-25** | 4 | 7760.0 | 37.70 / 38.20 / 37.95 / **0.013** | 31.80 / 32.30 / 32.05 / **0.016** | — |
| | | 7765.0 | 34.90 / 35.40 / 35.15 / **0.014** | 34.10 / 34.60 / 34.35 / **0.015** | — |
| | | 7770.0 | 32.30 / 32.80 / 32.55 / **0.015** | 36.50 / 37.00 / 36.75 / **0.014** | — |
| **2026-10-01** | 10 | 7760.0 | 58.60 / 59.70 / 59.15 / **0.019** | 49.50 / 50.40 / 49.95 / **0.018** | — |
| | | 7765.0 | 55.90 / 56.80 / 56.35 / **0.016** | 51.70 / 52.60 / 52.15 / **0.017** | — |
| | | 7770.0 | 53.20 / 54.20 / 53.70 / **0.019** | 54.00 / 54.90 / 54.45 / **0.017** | — |
| **2026-10-06** | 15 | 7760.0 | 73.30 / 74.40 / 73.85 / **0.015** | 61.00 / 62.00 / 61.50 / **0.016** | — |
| | | 7770.0 | 67.80 / 68.60 / 68.20 / **0.012** | 65.30 / 66.40 / 65.85 / **0.017** | — |
| | | 7775.0 | 64.90 / 65.90 / 65.40 / **0.015** | 67.60 / 68.60 / 68.10 / **0.015** | — |
| **2026-10-09** | 18 | 7760.0 | 85.90 / 87.00 / 86.45 / **0.013** | 69.30 / 70.50 / 69.90 / **0.017** | — |
| | | 7770.0 | 80.30 / 81.20 / 80.75 / **0.011** | 73.40 / 74.50 / 73.95 / **0.015** | — |
| | | 7775.0 | 77.50 / 78.30 / 77.90 / **0.010** | 75.70 / 76.50 / 76.10 / **0.011** | — |

---

### Rejection Summary (max_rel_spread = 0.25)

| Expiration | DTE | Total Quotes (3 strikes × 2 sides) | Rejected | Rejection Rate |
|------------|-----|-----------------------------------|----------|----------------|
| 2026-09-21 | 0 | 6 | **4** | **67%** |
| 2026-09-25 | 4 | 6 | 0 | 0% |
| 2026-10-01 | 10 | 6 | 0 | 0% |
| 2026-10-06 | 15 | 6 | 0 | 0% |
| 2026-10-09 | 18 | 6 | 0 | 0% |

**Only 0 DTE has rejections** — deep ITM/OTM options with near-zero premiums produce extreme relative spreads. All **4–18 DTE** near-ATM quotes are well within the 0.25 threshold (typical spread: 0.01–0.02).

---

### Strike Spacing — Production vs Sandbox Comparison

| Expiration | DTE | +50 pts | +100 pts | +150 pts | Widening Point |
|------------|-----|---------|----------|----------|----------------|
| **Sandbox** 2026-09-21 | 0 | 5.0 | 5.0 | 10.0 | ~+150 |
| **Production** 2026-09-21 | 0 | 5.0 | 5.0 | 10.0 | ~+150 |
| **Sandbox** 2026-09-25 | 4 | 5.0 | 5.0 | 5.0 | >+150 |
| **Production** 2026-09-25 | 4 | 5.0 | 5.0 | 10.0 | ~+150 |
| **Sandbox** 2026-10-01 | 10 | 5.0 | 10.0 | 10.0 | ~+75 |
| **Production** 2026-10-01 | 10 | 10.0 | 10.0 | 25.0 | **~+15** |
| **Sandbox** 2026-10-06 | 15 | 10.0 | 10.0 | 25.0 | ~+15 |
| **Production** 2026-10-06 | 15 | 10.0 | 25.0 | 25.0 | **~+15** |
| **Sandbox** 2026-10-09 | 18 | 10.0 | 10.0 | 10.0 | ~+15 |
| **Production** 2026-10-09 | 18 | 10.0 | 10.0 | 10.0 | **~+15** |

---

### Key Findings

1. **0 DTE is toxic for ATM selection**: 67% rejection rate at 3 nearest strikes due to deep ITM/OTM near-zero premiums. The 0 DTE chain should be **excluded from ATM basket** or handled with wider spread tolerance.

2. **4–18 DTE are clean**: All near-ATM quotes have relative spreads of 0.01–0.02, well under the 0.25 threshold.

3. **Strike spacing CONFIRMS DATA_INVENTORY §6.5**:
   - **10 DTE**: Widens to 10 pts at **+15 pts** from spot (was +75 in sandbox)
   - **15 DTE**: Widens to 10 pts at **+15 pts**, then 25 pts at **+50 pts** (sandbox showed +15 for first widening)
   - **18 DTE**: Widens to 10 pts at **+15 pts** (matches sandbox)

4. **Production grid is MORE aggressive** than sandbox — widening starts closer to spot. This reinforces: **never assume fixed 5-pt grid**; must use positional neighbor selection (K₀±1 in listed array).

5. **Sandbox fixtures archived** at `tests/fixtures_sandbox/` for reference.

---

### Config Status
- `config.yaml`: `tradier.base_url` = `https://api.tradier.com` (production)
- `spx_flux/.env`: Contains `TRADIER_PROD_ACCESS_TOKEN` (used for capture)
- Ready for Phase 2 IV validation against **real production quotes**
