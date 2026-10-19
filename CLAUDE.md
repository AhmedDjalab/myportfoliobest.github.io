# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Run development server (from myportfoliobest.github.io/)
python manage.py runserver

# Apply migrations
python manage.py migrate

# Create migrations after model changes
python manage.py makemigrations

# Create superuser for admin panel
python manage.py createsuperuser

# Collect static files (for deployment)
python manage.py collectstatic

# Run tests
python manage.py test
python manage.py test portfolio   # single app
```

## Architecture

Django 3.2 project with SQLite database, structured as two apps plus a `project/` config package:

- **`project/`** — Django settings, root URLconf, WSGI/ASGI. `settings.py` uses `whitenoise` for static file serving and has Heroku deployment code commented out.
- **`portfolio/`** — Main app. Models: `Contact` (contact form submissions), `Blogs` (blog posts with image uploads to `media/blog/`), `Internship` (intern application form, login-required), `Skills` (skill name + proficiency level). Views are function-based.
- **`authapp/`** — Authentication: signup/login/logout using Django's built-in `User` model; username field stores the email address.
- **`templates/`** — All HTML templates at the project root level (not per-app). `basic.html` is the base template extended by all pages.
- **`static/`** — Static assets (CSS, JS, images). Collected to `staticfiles/` for deployment.
- **`media/`** — User-uploaded files (blog images).

### URL structure
| Path | App | View |
|------|-----|------|
| `/` | portfolio | home |
| `/about` | portfolio | about |
| `/contact/` | portfolio | contact_view |
| `/blog` | portfolio | handleblog |
| `/skill` | portfolio | skill |
| `/internshipdetails` | portfolio | internshipdetails (login required) |
| `/auth/signup/` | authapp | signup |
| `/auth/login/` | authapp | handleLogin |
| `/auth/logout/` | authapp | handleLogout |
| `/admin/` | Django admin | — |

### Key notes
- `Internship` form deduplicates by USN and email, converts text fields to uppercase before saving.
- `authapp` uses email as the username (passed to `create_user` as both `username` and `email`).
- No requirements.txt is in the repo; dependencies include Django 3.2, whitenoise, gunicorn, and Pillow (for ImageField).
- `SECRET_KEY` in `settings.py` is the insecure development default — do not use in production without overriding via environment variable.
