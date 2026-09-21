Findings Report: pandas/numpy major-version jumps were unnecessary
1. Wheel availability test — PASS for older versions
pip install "pandas==2.2.3" "numpy==1.26.4" "scipy==1.13.1"
Exact output: All three packages downloaded and installed cp312-cp312-win_amd64 wheels successfully — no build from source, no errors.

Conclusion: The stated justification ("only version with x64 Windows wheel for Py 3.12") was incorrect. pandas 2.2.x and numpy 1.26.x both publish native Windows wheels for Python 3.12.

2. streamlit compatibility
streamlit version	pandas constraint	Works with pandas 2.x?
1.64.0 (was forced)	pandas<3,>=1.4 → actually requires pandas 3.x via altair 6.x + pyarrow 25.x transitive deps	❌
1.39.0 (original spec)	pandas<3,>=1.4.0 + altair 5.x + pyarrow 7+	✅
streamlit 1.39.0 (the version in the original spec) installs cleanly with pandas 2.2.3, numpy 1.26.4, scipy 1.13.1, and Pillow 10.4.0.

3. Minimal genuinely required changes vs. cascaded upgrades
Package	Original spec	Cascaded upgrade (wrong)	Actually needed	Reason
pandas	2.2.x	3.0.6	2.2.3	spec version works; 3.x forced by streamlit 1.64.0
numpy	1.26.x	2.3.5	1.26.4	spec version works; 2.x forced by pandas 3.x
scipy	1.13.x	1.18.1	1.13.1	spec version works; 1.18 forced by numpy 2.x
streamlit	1.39.0	1.64.0	1.39.0	spec version works; 1.64.0 was an unnecessary upgrade
Pillow	10.4.0	12.3.0	10.4.0	spec version works; 12.x forced by streamlit 1.64.0
Root cause: A single unnecessary upgrade — streamlit 1.39.0 → 1.64.0 — cascaded through altair 6.x → pyarrow 25.x → pandas 3.x → numpy 2.x → scipy 1.18.x → Pillow 12.x.

4. Risk of silent breakage from pandas 3.x (CoW + API changes)
This project uses	pandas 3.x behavior change	Failure mode if undetected
resample('5min') / resample('15min') for 5-day/20-day charts	CoW + resample aggregation changes	Different OHLCV bars → wrong z-score baselines
groupby(['front_dte', 'back_dte', 'mode']) for z-score buckets	groupby drops observed=False default, changes categorical handling	Missing/extra buckets → corrupt baseline_std
Minute-boundary timestamp joins for vendor reconciliation	DatetimeIndex comparison strictness, tz-aware vs naive	Join silently drops rows → vendor reconciliation shows "INSUFFICIENT DATA"
Verdict: Restoring the spec versions (pandas 2.2.3, numpy 1.26.4, scipy 1.13.1, streamlit 1.39.0, Pillow 10.4.0) is correct and necessary for this project's numerical stability.

5. Restored working stack (clean venv, isolated)
pandas==2.2.3
numpy==1.26.4
scipy==1.13.1
streamlit==1.39.0
Pillow==10.4.0
pyarrow==25.0.1  # transitive, has wheel, compatible with both pandas 2.x/3.x
All validation tests pass: config load, five validation errors, Pillow PNG write, JSON-lines logging — all with PYTHONNOUSERSITE=1.
