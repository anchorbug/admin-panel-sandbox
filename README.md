# admin-panel-sandbox
Training sandbox: launched via Docker, tested the API, connected to Postgres/PGAdmin.
# Admin Panel Sandbox (course)

This is a **training / sandbox** project from a course. I am **not the original author of the code** — I cloned it, ran it locally in Docker, connected to the database via PGAdmin, and tested the API with Postman.

The goal of this repo is to show how I can:
- spin up an existing project locally;
- understand the given requirements;
- map UI ↔ API ↔ database;
- create a basic test checklist from a textual spec.

---

## What I did

- Ran the project locally using **Docker**.
- Connected to **Postgres** via **PGAdmin** and checked the data in tables.
- Tested the backend endpoints via **Postman** (`/users`, `/user`, `/login`, etc.).
- Compared the actual UI with the course requirements (add user, list users, edit, delete, get token).
- Wrote a basic test checklist for the **User management** flow.
- Organized the course spec into `/docs`.

---

## Tech / tools

- Docker / docker-compose
- Postgres + PGAdmin
- Postman
- (optionally) React/HTML front-end from the course
- GitHub

---

## How to run locally

> These are sample commands — adjust ports/names to your local setup.

1. Clone the project:

   ```bash
   git clone https://github.com/<your-username>/admin-panel-sandbox.git
   cd admin-panel-sandbox
