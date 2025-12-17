# Railway Deployment Guide (Django Product API)

This guide deploys the JSON/XML Product API to Railway using SQLite for demo/grading. Total time: ~10–15 minutes.

## Prerequisites

- Project is in a Git repo (e.g., GitHub).
- Python 3.12+ locally (for any optional tweaks).
- Files already in repo: `requirements.txt` (Django, Pillow), codebase.

## Step 1: Add Procfile

Create `Procfile` in the repo root:

```bash
web: python manage.py migrate && python manage.py runserver 0.0.0.0:$PORT
```

## Step 2: (Optional) Settings tweaks

In `alvarez_bakery/settings.py`:

- Read secrets from env:

  ```python
  SECRET_KEY = os.getenv("SECRET_KEY", "dev-secret")
  DEBUG = os.getenv("DEBUG", "True").lower() == "true"
  ALLOWED_HOSTS = ["localhost", "127.0.0.1", ".up.railway.app"]
  ```

- If you later add Postgres, update `DATABASES` accordingly; for now SQLite is fine for demo.

## Step 3: Push to GitHub

Commit `Procfile` (and optional settings tweaks), then push to GitHub.

## Step 4: Create Railway service

1. Railway → New Project → Deploy from Repo → select your GitHub repo.
2. Build command: `pip install -r requirements.txt`
3. Start command: `python manage.py migrate && python manage.py runserver 0.0.0.0:$PORT`
4. Env vars (Project → Variables):
   - `SECRET_KEY` = any random string
   - `DEBUG` = True for demo, False for production
   - Optional: `ALLOWED_HOSTS` if you prefer to set it here (e.g., `.up.railway.app`)
5. No database add-on needed for demo (SQLite on ephemeral disk). For persistence, add Postgres and set `DATABASE_URL`/`DATABASES`.

## Step 5: Deploy and verify

- Wait for build → service status “Running.”
- Copy the public URL, e.g., `https://your-app.up.railway.app`
- Docs page: `https://your-app.up.railway.app/api/docs/`
- JSON list: `curl -H "Accept: application/json" https://your-app.up.railway.app/api/products/`
- XML list: `curl -H "Accept: application/xml" https://your-app.up.railway.app/api/products/`
- Create JSON:

  ```bash
  curl -X POST https://your-app.up.railway.app/api/products/ \
    -H "Content-Type: application/json" \
    -d "{\"name\":\"Sample Bread\",\"price\":5.25,\"stock\":10}"
  ```

- Create XML:

  ```bash
  curl -X POST "https://your-app.up.railway.app/api/products/?format=xml" \
    -H "Content-Type: application/xml" \
    -d "<product><name>Sample Bread</name><price>5.25</price><stock>10</stock></product>"
  ```

## Optional production hardening

- Set `DEBUG=False`.
- Ensure `ALLOWED_HOSTS` includes the Railway domain.
- Move to Postgres for persistence; run `python manage.py migrate` on startup or via a one-off command.
- Add static handling (WhiteNoise/CDN) and run `collectstatic` during build/start if serving static assets publicly.
