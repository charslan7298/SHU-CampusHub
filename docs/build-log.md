# SHU CampusHub – Build Log

## 2026-10-18 – Checkpoint 1–8: Base Django project setup

### What was done

| Step | Action | Outcome |
|------|--------|---------|
| CP1 | Verified Python 3.13.14 and Git 2.55 installed | ✅ Pass |
| CP2 | Created `.gitignore` (excludes `.venv/`, `*.pyc`, `.env`, `db.sqlite3`) | ✅ Pass |
| CP3 | Created `.venv` with `python -m venv .venv` | ✅ Pass |
| CP4 | Created `requirements.txt`; ran `pip install -r requirements.txt`; ran `django-admin startproject config .` | ✅ Django 5.2.18 installed; `manage.py` and `config/` created |
| CP5 | Created `.env` (DB_ENGINE=sqlite) and `.env.example`; updated `config/settings.py` with `load_dotenv()` and switchable `DATABASES` block | ✅ Pass |
| CP6 | Ran `python manage.py migrate` – all 18 migrations applied OK | ✅ Pass |
| CP7 | `git add . && git commit -m "Base Django project with switchable database"` | ✅ Committed |
| CP8 | This log created in `docs/build-log.md` | ✅ Complete |

### Errors & fixes

None – clean run.

### Django welcome page

Run `python manage.py runserver` and open <http://127.0.0.1:8000> to see the Django rocket welcome page.

### Notes

- `.env` is **not committed** (see `.gitignore`). Each developer copies `.env.example → .env` and sets their own values.
- Switch to PostgreSQL by changing `DB_ENGINE=postgres` in `.env` and populating the `DB_*` variables.
- Virtual environment activation (Windows PowerShell): `.venv\Scripts\Activate.ps1`
