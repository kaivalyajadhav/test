## Lockfile complete — 65 packages pinned

### requirements.in (direct deps, hand-edited)
```text
# Direct dependencies - human-chosen, hand-edited
# Regenerate lockfile (requirements.txt) with: pip freeze > requirements.txt
# from a clean Python 3.12 venv after installing from this file.

# Core data & compute
pandas==2.2.3
numpy==1.26.4
scipy==1.13.1

# Visualization & dashboard
streamlit==1.39.0
plotly==5.24.1
Pillow==10.4.0

# Configuration & validation
pydantic==2.10.6
pydantic-settings==2.5.2
PyYAML==6.0.2
python-dotenv==1.0.1

# HTTP & async
requests==2.32.3

# Scheduling & market calendar
APScheduler==3.10.4
pandas_market_calendars==4.4

# Testing
pytest==8.3.3
responses==0.25.3
```

### Key transitives explicitly pinned (cascade vectors)

| Package | Version | Role in cascade |
|---------|---------|-----------------|
| altair | 5.5.0 | streamlit → altair (blocks altair 6.x → pyarrow 25.x) |
| pyarrow | 25.0.1 | altair → pyarrow (compatible with pandas 2.x) |
| protobuf | 5.29.6 | streamlit → protobuf (abi3 wheel) |
| narwhals | 2.26.0 | altair → narwhals |
| tenacity | 8.5.0 | requests/retry stack |
| jsonschema | 4.26.0 | altair validation |
| packaging | 24.2 | build metadata |
| typing_extensions | 4.16.0 | pydantic core |

### Second throwaway venv test
- Created `.venv2`, installed strictly from `requirements.txt`
- `pip freeze` output **byte-identical** to first venv (65 packages, same versions)
- Config loads and passes all validation in both venvs with `PYTHONNOUSERSITE=1`

### Environment verification wired into `src/config.py`
```python
from scripts.verify_env import verify_environment

def load_config(...):
    lockfile_path = Path(__file__).parent.parent / "requirements.txt"
    verify_environment(lockfile_path)  # runs on every load
    ...
```
Fails loudly on:
- Python ≠ 3.12.x
- pandas major ≠ 2
- numpy major ≠ 1
- Any package missing/extra/version-mismatch vs lockfile
