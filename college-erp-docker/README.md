# Docker Based College ERP Deployment & Monitoring Platform

A demo project showing how a college ERP web application can be **containerized with
Docker** and **monitored** using cAdvisor + Prometheus + Grafana.

> Scope note: as instructed, this is **not** a full ERP with a database — it is a
> static, front-end-only ERP interface (Dashboard, Students, Attendance, Fees, Notices)
> with demo data, built to demonstrate **Docker deployment and monitoring skills**.

---

## 1. Project Structure

```
college-erp-docker/
├── app/                     # The ERP website (this gets containerized)
│   ├── index.html           # Dashboard
│   ├── students.html
│   ├── attendance.html
│   ├── fees.html
│   ├── notices.html
│   ├── monitoring.html
│   ├── assets/style.css
│   ├── Dockerfile           # Builds the ERP image (nginx + static site)
│   └── .dockerignore
├── monitoring/
│   └── prometheus.yml       # Tells Prometheus what to scrape
├── docker-compose.yml       # Runs ERP + monitoring stack together
└── README.md
```

---

## 2. Option A — Just the ERP (single container, simplest for submission)

This is enough to satisfy "runs in Docker, opens my ERP on any PC".

```bash
cd college-erp-docker/app

# Build the image
docker build -t college-erp:latest .

# Run it
docker run -d -p 8081:80 --name erp-web college-erp:latest
```

Open the browser at: **http://localhost:8081**

To stop/remove:
```bash
docker stop erp-web
docker rm erp-web
```

### Running it on ANY other PC (no internet needed there)

Export the image to a single portable file on your PC:
```bash
docker save -o college-erp.tar college-erp:latest
```
Copy `college-erp.tar` (pen drive / email / cloud) to the other PC, then there run:
```bash
docker load -i college-erp.tar
docker run -d -p 8081:80 --name erp-web college-erp:latest
```
Open **http://localhost:8081** — done. No Dockerfile, no source code, no internet
needed on the second PC — this is the real "portable Docker image" your teacher is
asking about.

---

## 3. Option B — Full stack with Monitoring (ERP + cAdvisor + Prometheus + Grafana)

This shows the "monitoring platform" part of the title using `docker-compose`.

```bash
cd college-erp-docker
docker compose up -d --build
```

This starts 4 containers:

| Service     | URL                      | Purpose                          |
|-------------|--------------------------|-----------------------------------|
| erp-web     | http://localhost:8081    | The ERP website                   |
| cadvisor    | http://localhost:8080    | Live container CPU/RAM/network stats |
| prometheus  | http://localhost:9090    | Stores metrics scraped from cAdvisor |
| grafana     | http://localhost:3000    | Dashboards (login: `admin` / `admin`) |

In Grafana: add a **Prometheus data source** pointing to `http://prometheus:9090`,
then build/import a dashboard using cAdvisor metrics (e.g. `container_cpu_usage_seconds_total`,
`container_memory_usage_bytes`) to show live monitoring of the ERP container — this is
your "monitoring platform" proof for the viva.

Stop everything:
```bash
docker compose down
```

---

## 4. What this project demonstrates (for viva/demo)

- Writing a **Dockerfile** (base image, COPY, EXPOSE, CMD, HEALTHCHECK)
- **Building** and **running** a custom Docker image
- **Port mapping** (host ↔ container)
- **docker save / docker load** — exporting an image to run on any machine, offline
- **docker-compose** — multi-container orchestration with a shared network
- **Container monitoring** — using cAdvisor to expose metrics, Prometheus to
  scrape/store them, and Grafana to visualize them
- Keeping the app itself deliberately simple (static HTML/CSS, no DB) so the
  Docker/monitoring work stays the clear focus of the project

---

## 5. Notes

- No database is used anywhere — all ERP data (students, attendance, fees, notices)
  is hard-coded demo data in the HTML files, as required.
- The `cadvisor` container mounts host system paths read-only (standard official
  usage) — this is normal for cAdvisor and does not modify your system.
- Default Grafana login is `admin` / `admin` — change it if you present this in
  front of others.

## Run from Docker Hub
docker pull jigna127/college-erp:latest
docker run -d -p 8081:80 --name erp-web jigna127/college-erp:latest
Open: http://localhost:8081