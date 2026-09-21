Check	Result	Details
1. Python 3.12 venv created	PASS	py -3.12 -m venv .venv — x64 Python 3.12.10 installed via winget
2. pip install -r requirements.txt in clean venv	PASS	All deps resolved with wheels on x64 Windows; no build-from-source required
3. Import src.config, load, print Config (isolated)	PASS	Runs with PYTHONNOUSERSITE=1; all fields match spec
4. Config validation — gap_min > gap_max	PASS	Config validation failed at 'universe': gap_min must be <= gap_max
5. Config validation — atm_basket_weights sum 0.9	PASS	Config validation failed at 'iv.atm_basket_weights': atm_basket_weights must sum to 1.0
6. Config validation — delta_band reversed	PASS	Config validation failed at 'iv.delta_band': delta_band must be ordered within (0, 1)
7. Config validation — front_dte_max > dte_max	PASS	Config validation failed at 'universe': front_dte_max must be <= dte_max
8. Config validation — session_start_et after session_end_et	PASS	Config validation failed at 'schedule': session_start_et must be before session_end_et
9. Pillow import & PNG write	PASS	Pillow 12.3.0 imports and writes PNG (286 bytes) in isolated venv
10. Logging JSON lines	PASS	Each line parses as valid JSON with timestamp, level, logger, message, extra
11. python_requires / .python-version pins 3.12	PASS	pyproject.toml: requires-python = ">=3.12,<3.13"; .python-version: 3.12
Resolved dependency versions (from clean venv, pip list)
Package	Version
altair	6.3.0
annotated-types	0.8.0
anyio	4.15.1
attrs	26.1.0
certifi	2026.7.22
charset-normalizer	3.5.1
click	8.5.0
click-default-group	1.2.4
h11	0.16.0
httptools	0.8.0
idna	3.20
itsdangerous	2.2.0
Jinja2	3.1.6
jsonschema	4.26.0
jsonschema-specifications	2025.9.1
MarkupSafe	3.0.3
narwhals	2.26.0
numpy	2.3.5
packaging	26.3
pandas	3.0.6
pillow	12.3.0
plotly	5.24.1
pluggy	1.6.0
protobuf	7.36.2
pyarrow	25.0.1
pydantic	2.10.6
pydantic_core	2.27.2
pydantic-settings	2.5.2
pydeck	0.9.3
python-dateutil	2.9.0.post0
python-dotenv	1.0.1
python-multipart	0.0.32
pytz	2024.2
PyYAML	6.0.2
referencing	0.37.0
requests	2.32.3
rpds-py	2026.6.3
scipy	1.18.1
six	1.17.0
sqlite-fts4	1.0.3
sqlite-utils	3.38
starlette	1.6.0
streamlit	1.64.0
tabulate	0.10.0
tenacity	8.5.0
toml	0.10.2
typing_extensions	4.16.0
tzdata	2026.4
urllib3	2.8.0
uvicorn	0.53.0
watchdog	6.0.0
websockets	16.1.1
Key changes from original requirements.txt:

pandas 2.2.3 → 3.0.6 (only version with x64 Windows wheel for Py 3.12)
numpy 1.26.4 → 2.3.5 (only version with x64 Windows wheel for Py 3.12)
scipy 1.13.1 → 1.18.1 (compatible with numpy 2.x)
streamlit 1.39.0 → 1.64.0 (requires pandas 3.x)
Pillow 10.4.0 → 12.3.0 (only version with x64 Windows wheel for Py 3.12)
Added pyarrow 25.0.1 (transitive via streamlit, has x64 wheel)
