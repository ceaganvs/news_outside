# News Outside

Django news publishing platform with role-based access control, publisher management,
reader subscriptions, and a RESTful API.

## Features

- **3 User Roles**: Reader, Journalist, Editor
- **Publishers**: Editors create publishers; journalists and editors belong to a publisher
- **Article Workflow**: Independent journalists publish directly; publisher-affiliated articles require editor approval
- **Subscriptions**: Readers subscribe to publishers or individual journalists
- **Email Notifications**: Subscribers are emailed when a new article is approved or published
- **Newsletters**: Curated article collections created by journalists and editors
- **RESTful API**: Token authentication, full CRUD, role-based access
- **X (Twitter) Integration**: Posts to X when an article is approved (requires API credentials)

## Documentation

Generated Sphinx documentation is in `docs/build/html/`. Open
`docs/build/html/index.html` in a browser to browse the full API reference.

---

## Option 1 — Virtual Environment

```powershell
git clone https://github.com/ceaganvs/news_outside.git
cd news_outside

python -m venv venv
venv\Scripts\activate         # Windows
# source venv/bin/activate    # macOS / Linux

pip install -r requirements.txt
python manage.py migrate
python manage.py setup_groups
python manage.py createsuperuser
python manage.py runserver
```

Open http://127.0.0.1:8000/

---

## Option 2 — Docker

Requires Docker and Docker Compose.

```bash
git clone https://github.com/ceaganvs/news_outside.git
cd news_outside

docker compose up --build

# First run — set up groups and a superuser
docker compose exec web python manage.py setup_groups
docker compose exec web python manage.py createsuperuser
```

Open http://127.0.0.1:8000/

```bash
docker compose down        # stop
docker compose down -v     # stop and remove database volume
```

### Secrets and Environment Variables

The `docker-compose.yml` file uses placeholder development values. For production,
replace them with strong secrets and **never commit real credentials to a public repo**.

| Variable | Purpose |
|---|---|
| `SECRET_KEY` | Django secret key |
| `DB_PASSWORD` | MariaDB app-user password |
| `MARIADB_ROOT_PASSWORD` | MariaDB root password |

---

## X (Twitter) API Configuration

Set these environment variables before starting the server to enable X posting.

**PowerShell (venv):**
```powershell
$env:X_API_CONSUMER_KEY        = "your_consumer_key"
$env:X_API_CONSUMER_SECRET     = "your_consumer_secret"
$env:X_API_ACCESS_TOKEN        = "your_access_token"
$env:X_API_ACCESS_TOKEN_SECRET = "your_access_token_secret"
```

**Docker Compose** — add to the `web` service `environment` block:
```yaml
X_API_CONSUMER_KEY: your_consumer_key
X_API_CONSUMER_SECRET: your_consumer_secret
X_API_ACCESS_TOKEN: your_access_token
X_API_ACCESS_TOKEN_SECRET: your_access_token_secret
```

Credentials come from your app at [developer.x.com](https://developer.x.com).
Requires **Read and Write** permissions. If not set, the server runs normally and
logs a message instead of posting.

---

## Running Tests

```bash
python manage.py test NOPE
```

## API Endpoints

| Method | Endpoint | Access |
|---|---|---|
| POST | `/api/token/` | Anyone |
| GET | `/api/articles/` | Public |
| POST | `/api/articles/` | Journalists |
| PUT/PATCH | `/api/articles/<id>/` | Author or Editor |
| DELETE | `/api/articles/<id>/` | Editor |
| POST | `/api/articles/<id>/approve/` | Editors |
| GET | `/api/articles/subscribed/` | Readers |
| GET/POST | `/api/newsletters/` | Authenticated |
| GET | `/api/publishers/` | Authenticated |
| GET | `/api/users/me/` | Authenticated |
| POST | `/api/users/<id>/subscribe_publisher/` | Readers |
| POST | `/api/users/<id>/subscribe_journalist/` | Readers |
