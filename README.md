# News Outside

Django news publishing platform with role-based access control, publisher management, reader subscriptions, and a RESTful API.

## Features

- **3 User Roles**: Reader, Journalist, Editor
- **Publishers**: Editors create publishers; journalists and editors belong to a publisher
- **Article Workflow**: Independent journalists publish directly; publisher-affiliated articles require editor approval
- **Subscriptions**: Readers subscribe to publishers or individual journalists
- **Email Notifications**: Subscribers are emailed when a new article is approved or published
- **Newsletters**: Curated article collections created by journalists and editors
- **RESTful API**: Token authentication, full CRUD, role-based access
- **X (Twitter) Integration**: Posts to X when an article is approved (requires API credentials)

## Setup

```powershell
# Install dependencies
pip install -r requirements.txt

# Run migrations
python manage.py migrate

# Set up groups and permissions
python manage.py setup_groups

# Create superuser
python manage.py createsuperuser

# Run development server
python manage.py runserver
```

## X (Twitter) API Configuration

To enable posting to X when articles are approved, set the following environment variables before starting the server.

**Windows (PowerShell):**
```powershell
$env:X_API_CONSUMER_KEY     = "your_consumer_key"
$env:X_API_CONSUMER_SECRET  = "your_consumer_secret"
$env:X_API_ACCESS_TOKEN     = "your_access_token"
$env:X_API_ACCESS_TOKEN_SECRET = "your_access_token_secret"
```

**Linux / macOS:**
```bash
export X_API_CONSUMER_KEY="your_consumer_key"
export X_API_CONSUMER_SECRET="your_consumer_secret"
export X_API_ACCESS_TOKEN="your_access_token"
export X_API_ACCESS_TOKEN_SECRET="your_access_token_secret"
```

These credentials come from your app at [developer.x.com](https://developer.x.com). You need a project with **Read and Write** permissions. If the variables are not set the server still runs normally — it will just log a message instead of posting to X.

## API Endpoints

| Method | Endpoint | Access |
|--------|----------|--------|
| POST | `/api/token/` | Anyone — returns auth token |
| GET | `/api/articles/` | Public (approved articles) |
| POST | `/api/articles/` | Journalists only |
| GET | `/api/articles/<id>/` | Public |
| PUT/PATCH | `/api/articles/<id>/` | Author or Editor |
| DELETE | `/api/articles/<id>/` | Editor only |
| POST | `/api/articles/<id>/approve/` | Editors only |
| GET | `/api/articles/subscribed/` | Readers — returns subscribed content |
| GET | `/api/newsletters/` | Authenticated users |
| POST | `/api/newsletters/` | Journalists and Editors |
| GET | `/api/publishers/` | Authenticated users |
| GET | `/api/users/me/` | Authenticated users |
| POST | `/api/users/<id>/subscribe_publisher/` | Readers only |
| POST | `/api/users/<id>/subscribe_journalist/` | Readers only |

## Web Routes

- `/` — Latest approved articles (landing page)
- `/register/` — Create account (reader, journalist, or editor)
- `/login/` — Login
- `/dashboard/` — Role-specific dashboard
- `/publishers/` — Browse all publishers
- `/publishers/create/` — Create publisher (editors only)
- `/subscriptions/` — Manage subscriptions (readers only)
- `/articles/create/` — Write article (journalists only)
- `/editor/pending/` — Review pending articles (editors only)

## Article Approval Rules

- **Independent journalist** (no publisher selected): article publishes immediately and subscribers are notified by email.
- **Publisher-affiliated article**: article enters a pending queue and requires approval from an editor before publishing.

## Running Tests

```powershell
python manage.py test NOPE
```

23 automated tests covering models, API endpoints, permissions, signals, and privacy settings.

## Access

- Web: http://127.0.0.1:8000/
- Admin: http://127.0.0.1:8000/admin/
- API browser: http://127.0.0.1:8000/api/
