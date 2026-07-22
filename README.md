# StayNest 🔑

**Find bachelor rooms, PGs, and flats — or list your own.**

StayNest is a full-stack web app for the Indian rental market where room-seekers
(students, bachelors, working professionals) can search for accommodation by
city/locality/budget, and property owners ("partners") can list rooms with
photos and manage inquiries.

Built with **Django** (backend, templates, auth) + **HTML/CSS/JavaScript** (frontend).

---

## Features

- 🔍 Search & filter rooms by city, locality, room type, gender preference, and max rent
- 🏠 Partner accounts can list rooms with up to 6 photos, rent, deposit, and amenities
- 💬 Seekers can send inquiries directly to the room owner (no middleman/brokerage)
- 📊 Partner dashboard — manage listings, see inquiry counts, edit/delete rooms
- 👤 Two account types: **Room Seeker** and **Partner / Owner** (chosen at signup)
- 📱 Fully responsive, custom-designed UI (no template frameworks like Bootstrap)
- 🖼️ Image upload support for room listings (via Pillow)

---

## Tech Stack

| Layer      | Tech                                   |
|------------|-----------------------------------------|
| Backend    | Django 6 (Python)                       |
| Frontend   | HTML5, CSS3 (custom design system), vanilla JS |
| Database   | SQLite (dev) / PostgreSQL (production)  |
| Auth       | Django's built-in auth, custom User model with roles |
| Static/Media | Whitenoise (static), Django media storage (uploads) |
| Deployment | Gunicorn + Whitenoise, ready for Render / Railway / PythonAnywhere |

---

## Project Structure

```
staynest/
├── accounts/            # Custom User model (seeker/partner roles), signup/login views
├── listings/            # Room, RoomImage, Inquiry models + views + templates
│   └── management/commands/seed_demo_data.py   # sample data seeder
├── staynest/            # Project settings, root urls
├── templates/           # base.html + app templates
├── static/               # css/style.css, js/main.js
├── media/                # uploaded room photos (created at runtime)
├── requirements.txt
├── Procfile              # for Render/Railway/Heroku-style deploys
├── .env.example          # copy to .env and fill in your own values
└── manage.py
```

---

## Local Setup

**1. Clone/unzip the project and enter it:**
```bash
cd staynest
```

**2. Create a virtual environment and install dependencies:**
```bash
python3 -m venv venv
source venv/bin/activate        # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

**3. Set up your environment file:**
```bash
cp .env.example .env
```
For local development the defaults in `.env.example` work as-is (SQLite, DEBUG=True).

**4. Run migrations:**
```bash
python manage.py migrate
```

**5. (Optional but recommended) Seed demo data — a partner account + 4 sample rooms:**
```bash
python manage.py seed_demo_data
```
This creates:
- Superuser: `admin` / `staynest123`
- Demo partner: `rahul_partner` / `staynest123`

**⚠️ Change these passwords immediately if you deploy this publicly.**

**6. Create your own superuser (optional, if you skipped seeding):**
```bash
python manage.py createsuperuser
```

**7. Run the dev server:**
```bash
python manage.py runserver
```
Visit **http://127.0.0.1:8000/**

Admin panel: **http://127.0.0.1:8000/admin/**

---

## How It Works

1. **Sign up** at `/accounts/signup/` and choose **Room Seeker** or **Partner / Owner**.
2. **Partners** can go to **List a Room** (or their **Dashboard**) to add a listing —
   title, description, room type, location, rent, deposit, amenities, and photos.
3. **Seekers** browse `/rooms/`, filter by city/locality/budget/type, open a room's
   detail page, and send an inquiry message directly to the owner.
4. Partners see all inquiries and manage their listings from `/dashboard/`.

---

## Deploying

### Option A — Render (recommended, free tier available)

1. Push this project to a GitHub repo.
2. Create a new **Web Service** on [render.com](https://render.com), connect the repo.
3. Build command: `pip install -r requirements.txt && python manage.py collectstatic --noinput`
4. Start command: `gunicorn staynest.wsgi --bind 0.0.0.0:$PORT`
5. Add a **PostgreSQL** instance on Render and copy its `DATABASE_URL` into your
   Web Service's environment variables, along with:
   - `SECRET_KEY` (generate a long random string)
   - `DEBUG=False`
   - `ALLOWED_HOSTS=your-app-name.onrender.com`
6. Render auto-runs `release: python manage.py migrate` from the `Procfile`.
7. After the first deploy, run `python manage.py seed_demo_data` from Render's shell
   (or `createsuperuser`) to get an admin login.

### Option B — Railway

1. Push to GitHub, then **New Project → Deploy from GitHub repo** on
   [railway.app](https://railway.app).
2. Add a **PostgreSQL** plugin — Railway sets `DATABASE_URL` automatically.
3. Set environment variables: `SECRET_KEY`, `DEBUG=False`,
   `ALLOWED_HOSTS=your-app.up.railway.app`.
4. Railway uses the `Procfile` automatically for the web process.

### Option C — PythonAnywhere (simplest for students)

1. Upload the project (or `git clone` it) in a Bash console.
2. Create a virtualenv and `pip install -r requirements.txt`.
3. Set up a new Web App (Manual configuration, Python 3.12), point the WSGI file
   to `staynest.wsgi.application`, set the working directory, and add a static
   files mapping for `/static/` → `staticfiles/` and `/media/` → `media/`.
4. Run `python manage.py migrate` and `python manage.py collectstatic` from the console.

**Before any production deploy:**
- Set `DEBUG=False`
- Set a strong, unique `SECRET_KEY`
- Set `ALLOWED_HOSTS` to your real domain
- Change the seeded `admin` / `rahul_partner` passwords (or delete/skip seeding)

---

## Notes

- Room photos are stored via Django's `ImageField`; on most free hosting tiers
  (Render, Railway) the filesystem is **ephemeral** — for production-grade image
  persistence, connect an S3-compatible bucket (e.g. via `django-storages`) so
  uploads survive restarts/redeploys.
- This is set up as a learning/demo project — review Django's
  [deployment checklist](https://docs.djangoproject.com/en/stable/howto/deployment/checklist/)
  before using it for anything beyond a portfolio/college submission.
