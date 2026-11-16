# Khanyisi Construction

Responsive React website and Spring Boot / PostgreSQL quote request API.

## Run the complete website locally

This Windows setup uses the installed PostgreSQL service on `localhost:5432`. It expects PostgreSQL 17, Node.js, Java 21+, and Maven available on this machine. From PowerShell in this folder, run:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\start-local.ps1
```

The script securely prompts for your PostgreSQL administrator username and password, creates the `khanyisi` database and a dedicated `khanyisi_app` role if needed, builds React, packages it into Spring Boot, starts the API, waits for `/api/health`, and opens `http://localhost:8080`. The administrator password is not saved. The app role’s random password is encrypted for your Windows account in the ignored `.local-postgres` folder. The site and quote API are served from that one address. To stop the app, run `stop-local.ps1` from this folder.

For deployment, set `DATABASE_URL`, `DATABASE_USERNAME`, and `DATABASE_PASSWORD` to the production PostgreSQL connection.

If you have Docker Desktop, start the included PostgreSQL 16 container:

```powershell
docker compose up -d postgres
```

For a separate application database and user, connect using a database administrator account and run:

```sql
CREATE USER khanyisi_app WITH PASSWORD 'replace-with-a-local-secret';
CREATE DATABASE khanyisi OWNER khanyisi_app;
```

Then set the API connection variables in the current PowerShell session and start Spring Boot:

```powershell
$env:DATABASE_URL = "jdbc:postgresql://localhost:5432/khanyisi"
$env:DATABASE_USERNAME = "khanyisi_app"
$env:DATABASE_PASSWORD = "replace-with-a-local-secret"
mvn spring-boot:run
```

The API listens on `http://localhost:8080`. Quote requests are saved to the `quote_requests` table at `POST /api/quotes`; `GET /api/health` is a simple health check. The frontend uses the same-origin `/api` path.

## Start the frontend

```powershell
cd frontend
npm install
npm run dev
```

For standalone Vite development, run Spring Boot at port 8080 and then run Vite. Vite proxies `/api` to Spring Boot; the production frontend uses the same `/api` path and is packaged into the backend jar.
