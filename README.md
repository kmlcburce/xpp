# ☁️ Weather Data Platform (XPP)

A containerized full-stack solution to collect, transform, and deliver weather forecast data using Laravel, React, PostgreSQL, and Docker. CI/CD and testing are powered by GitHub Actions.

---

## 🚀 Project Architecture

| Layer        | Tech Stack                  | Description                                                 |
|--------------|-----------------------------|-------------------------------------------------------------|
| Frontend     | React (Vite), Tailwind CSS  | Displays weather reports, handles downloads + user actions |
| Backend API  | Laravel 10                  | Handles data fetching, transformation, S3 upload, and logs |
| Database     | PostgreSQL                  | Stores forecast logs and report metadata                   |
| Infrastructure | Docker + Docker Compose   | Isolated containers for dev & prod, Laravel, React, DB     |
| CI/CD        | GitHub Actions              | Automated build, test, and future deploy pipelines          |

---

## 🔨 Installation
```bash
git clone https://github.com/kmlcburce/xpp/
```
Inside the /xpp folder, run
```
git clone https://github.com/kmlcburce/xpp-react
git clone https://github.com/kmlcburce/xpp-laravel
```
## 📦 Services via Docker Compose

| Service         | Port   | Description                         |
|------------------|--------|-------------------------------------|
| Laravel API      | `:8080`| Serves `/api/forecast`, handles jobs|
| React Frontend   | `:3000`| Vite dev server (dev)               |
| PostgreSQL       | `:5432`| Database for Laravel API            |
| Nginx            | `:8080`| Reverse proxy + routing to Laravel  |
| React Dev Server | `:5173`| Dev hot reload for React            |

```bash
docker compose up --build
```
⚙️ Laravel Setup
API Endpoints
| Endpoint                 | Method | Description                              |
| ------------------------ | ------ | ---------------------------------------- |
| `/api/forecast/fetch`    | GET    | Fetch live data from HK Observatory      |
| `/api/forecast/generate` | GET    | Fetch + Convert + Upload forecast as CSV |
| `/api/forecast/logs`     | GET    | Return past forecast logs from DB        |
/generate?type=current or type=9day - supports two modes

File Uploads
- CSV is generated using 9-day forecast or current day forecast
- Temperature is converted to Fahrenheit from Celsius 
- Files are uploaded to S3 using Laravel’s Storage facade
- Report metadata (type, filename, URL) is saved to the DB

🧪 Testing Setup (CI/CD)
Laravel Tests
Uses PostgreSQL in CI
.env.testing and .env are auto-generated
Runs ```php artisan test``` after migrations

React Tests
Uses vitest
Headless and CI-optimized

```
npm run test
```

GitHub Actions
React + Laravel CI workflows run on:

- Pushes to master

- Pull requests

Laravel service runs migrations + API tests

React app installs and tests using Vite + Vitest

🧊 Environment Variables (Sample)
```
.env (Laravel)
env
Copy
Edit
APP_ENV=local
APP_KEY=base64:...
DB_CONNECTION=pgsql
DB_HOST=db
DB_PORT=5432
DB_DATABASE=xpp_pgsql
DB_USERNAME=laravel_user
DB_PASSWORD=P@ssw0rd
AWS_ACCESS_KEY_ID=your_key
AWS_SECRET_ACCESS_KEY=your_secret
AWS_DEFAULT_REGION=your_region
AWS_BUCKET=your_bucket
FILESYSTEM_DISK=s3
```
🐳 Docker Compose Overview
```
services:
  db:
    image: postgres:15
  laravel:
    build: ./xpp-laravel
  nginx:
    image: nginx:alpine
    ports:
      - "8080:80"
  react-dev:
    build:
      context: ./xpp-react
      dockerfile: Dockerfile.dev
    ports:
      - "5173:5173"
  react-prod:
    build:
      context: ./xpp-react
      dockerfile: Dockerfile.prod
    ports:
      - "3000:80"
```
🔁 React Routing & Page Refresh Fix
- nginx/default.conf has been modified to accommodate page refreshes
```
location /app {
    root /usr/share/nginx/html;
    index index.html;
    try_files $uri /index.html;
}
```
🧠 Credits

kmlcburce – Full-stack development, CI, Docker, and architecture

HK Observatory – Weather API

🧰 Commands Cheat Sheet

| Command                            | Purpose                          |
| ---------------------------------- | -------------------------------- |
| `docker compose up --build`        | Rebuild and start all containers |
| `docker compose down`              | Tear down the containers         |
| `docker compose exec laravel bash` | Enter Laravel container          |
| `php artisan migrate`              | Run migrations                   |
| `php artisan test`                 | Run Laravel tests                |
| `npm run dev` / `npm run build`    | React dev/prod build             |
| `npm run test`                     | Run React Vitest tests           |

📬 Contact
For questions or contributions, open an issue or ping the maintainer.

✈️ Deployment
Currently active at
```
http://35.209.5.171:3000/
```
