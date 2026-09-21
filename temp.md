Capture Complete — Live Tradier Sandbox Fixtures
Fixtures Created
File	Description
tests/fixtures/spx_quote.json	SPX spot quote (bid 7732.0 / ask 7735.45, last 7733.7)
tests/fixtures/spx_expirations.json	58 total expirations
tests/fixtures/spx_chain_2026-09-21.json	0 DTE — 488 options (244 strikes)
tests/fixtures/spx_chain_2026-09-25.json	4 DTE — 610 options (305 strikes)
tests/fixtures/spx_chain_2026-10-01.json	10 DTE — 394 options (197 strikes)
tests/fixtures/spx_chain_2026-10-06.json	15 DTE — 364 options (182 strikes)
tests/fixtures/spx_chain_2026-10-09.json	18 DTE — 444 options (222 strikes)
tests/fixtures/CAPTURE_MANIFEST.txt	Timestamped manifest
All captured within 2.4 seconds (well within 60-second window). All chains contain bid, ask, strike, option_type.

Strike Spacing Analysis (Spot = 7733.7)
Expiration	DTE	+50 pts	+100 pts	+150 pts	Pattern near spot
2026-09-21	0	5.0	5.0	10.0	5.0 pts throughout ±150
2026-09-25	4	5.0	5.0	5.0	5.0 pts throughout ±150
2026-10-01	10	5.0	10.0	10.0	5.0 → 10.0 at +75 pts
2026-10-06	15	10.0	10.0	25.0	5.0 → 10.0 at +15 pts → 25.0 at +175 pts
2026-10-09	18	10.0	10.0	10.0	5.0 → 10.0 at +15 pts
Key Findings — DATA_INVENTORY §6.5 CONFIRMED
15 expirations in 0–20 DTE (vs 58 total) — daily SPXW weeklies provide dense coverage
Typical strike count: 182–305 unique strikes per chain
Non-uniform grid confirmed:
0–4 DTE: 5.0 pt spacing holds to ~±150 pts from spot
10 DTE: widens to 10.0 pts at ~+75 pts from spot
15 DTE: widens to 10.0 pts at ~+15 pts, then 25.0 pts at +175 pts
18 DTE: widens to 10.0 pts at ~+15 pts
Critical for §5.1: snap_strike(grid=5.0) would fail for 10+ DTE chains — strikes at 7785, 7835, 7890 do not exist at fixed 5-pt intervals. Must use positional neighbor selection (K₀±1 in listed array).
Config Restored
config.yaml: tradier.base_url → https://api.tradier.com (production)
Ready for live use with production token
