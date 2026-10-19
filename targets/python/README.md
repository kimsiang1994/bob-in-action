# Python / FastAPI (invoice preview)

Bob's bounded proof that the invoice-preview feature can be rebuilt as a FastAPI service (`app/main.py`, route `POST /api/invoice-preview`), with unittest contract tests in `tests/`. It is one feature, not a full migration.

## Build and test

Needs Python 3.11 or later. Run from `targets/python`:

```powershell
cd targets/python
python -m venv .venv
.venv\Scripts\python -m pip install -r requirements.txt   # macOS/Linux: .venv/bin/python
.venv\Scripts\python -m compileall -q app
.venv\Scripts\python -m unittest discover -s tests -v
```

Verified here: 36 tests, `OK`, on Python 3.11.9 with the pinned FastAPI 0.143.1, httpx 0.28.1 and uvicorn 0.54.0 (an existing environment with those versions was used; the `pip install` step itself was not rerun).

## Run

`.venv\Scripts\python -m uvicorn app.main:app --host 127.0.0.1 --port 18084`
