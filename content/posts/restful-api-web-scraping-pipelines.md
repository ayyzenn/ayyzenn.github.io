---
title: "RESTful API with Web Scraping: My FastAPI + Flask Project"
author: "Saad"
date: 2026-03-07
tags: ["python", "fastapi", "flask", "web-scraping", "postgresql", "mongodb", "rest-api", "beautifulsoup", "selenium"]
categories: ["Others"]
---
## What This Project Is

I built a backend that scrapes websites, stores data in PostgreSQL and MongoDB, and exposes REST APIs through FastAPI and Flask. I made it as a portfolio piece — something I could demo and actually run on my machine.

---

## 1. What Does This Project Do? (Simple Explanation)

**This project is a data aggregator backend.**

It can:

- **Scrape** product or article data from websites
- **Store** that data in two databases (PostgreSQL for structured data, MongoDB for flexible logs)
- **Expose REST APIs** so other apps can create, read, update, and delete data
- **Call external APIs** and return the results

You can think of it as:

> A small backend that collects data from the web, saves it, and lets you access it through simple HTTP endpoints.

---

## 2. Technologies Used

| Technology | Purpose |
| ---------- | ------- |
| **FastAPI** | Main REST API – fast, modern, auto-generates docs |
| **Flask** | Second API for data processing and proxying |
| **BeautifulSoup** | Scrape static HTML pages (no browser needed) |
| **Selenium** | Scrape JavaScript-heavy pages (uses headless Chrome) |
| **PostgreSQL** | Relational database for structured product data |
| **MongoDB** | Document database for flexible scrape logs |
| **SQLAlchemy** | Connect to PostgreSQL and run queries |
| **PyMongo** | Connect to MongoDB and store documents |

---

## 3. What You Need Before Starting

Make sure you have:

- **Python 3.11 or 3.12** (recommended — some packages may not support the newest Python yet)
- **Docker and Docker Compose** (for PostgreSQL and MongoDB)
- **Git** (for version control)

You do **not** need to install PostgreSQL or MongoDB on your system. Docker will run them in containers.

---

## 4. Project Structure (Overview)

After setup, your project will look like this:

```text
backend/
├── app/                    # FastAPI main application
│   ├── main.py             # All REST endpoints
│   ├── schemas.py          # Data validation (Pydantic)
│   └── crud.py             # Database operations
├── flask_service/          # Flask microservice
│   └── app.py              # Text processing, proxy endpoints
├── scrapers/               # Web scraping code
│   ├── beautifulsoup_scraper.py
│   ├── selenium_scraper.py
│   └── demo_scraper.py
├── db/                     # Database setup
│   ├── postgres.py         # PostgreSQL connection
│   ├── mongodb.py          # MongoDB connection
│   ├── models.py           # Table definitions
│   └── init_db.py          # Create tables
├── config.py               # Settings (env variables)
├── docker-compose.yml      # PostgreSQL + MongoDB
├── requirements.txt       # Python packages
└── README.md
```

---

## 5. Step 1: Create a Virtual Environment

A virtual environment keeps your project’s packages separate from the rest of your system.

```bash
cd ~/Desktop/backend
python -m venv venv
```

Activate it:

```bash
# On Linux or Mac:
source venv/bin/activate

# On Windows:
venv\Scripts\activate
```

You should see `(venv)` at the start of your terminal line. That means the virtual environment is active.

---

## 6. Step 2: Install Dependencies

Install all required Python packages:

```bash
pip install -r requirements.txt
```

This installs FastAPI, Flask, BeautifulSoup, Selenium, SQLAlchemy, PyMongo, and other libraries.

If you get errors with `psycopg2` or `pydantic`, try using Python 3.11 or 3.12. Some packages may not fully support the newest Python yet.

---

## 7. Step 3: Start the Databases with Docker

PostgreSQL and MongoDB run inside Docker containers. You do not need to install them on your computer.

Start both databases:

```bash
docker-compose up -d
```

The `-d` flag runs them in the background. You should see something like:

```text
Container backend-postgres-1  Started
Container backend-mongodb-1  Started
```

Check that they are running:

```bash
docker ps
```

You should see two containers: one for PostgreSQL (port 5432) and one for MongoDB (port 27017).

---

## 8. Step 4: Create the Database Tables

PostgreSQL needs tables before it can store data. Run the init script:

```bash
python -m db.init_db
```

You should see:

```text
PostgreSQL tables created successfully!
```

This creates a `products` table with columns for title, price, description, and source URL.

---

## 9. Step 5: Run the FastAPI Server

Start the main API:

```bash
uvicorn app.main:app --reload --port 8000
```

The `--reload` flag restarts the server when you change the code. Good for development.

You should see:

```text
Uvicorn running on http://127.0.0.1:8000
Application startup complete.
```

Open your browser and go to:

```text
http://localhost:8000
```

You will see a welcome message with links to the API docs and endpoints.

---

## 10. Step 6: Open the Interactive API Docs

FastAPI automatically generates documentation. Go to:

```text
http://localhost:8000/docs
```

This is the **Swagger UI**. You can:

- See all endpoints (GET, POST, PUT, DELETE)
- Try them out directly in the browser
- Send requests and see responses

No need to use `curl` or Postman while learning. The docs page does it for you.

---

## 11. Step 7: Try the Scraping Endpoint

The project includes a **demo scraper** that uses a practice website called [books.toscrape.com](https://books.toscrape.com). It does not need Chrome or Selenium.

In the Swagger UI at `/docs`:

1. Find **POST /scrape**
2. Click **Try it out**
3. Leave the body as `{}` (empty JSON)
4. Click **Execute**

The API will:

- Scrape book titles and prices from the demo site
- Save them in PostgreSQL
- Log the scrape in MongoDB

Then open **GET /products** and click **Try it out** → **Execute**. You will see the scraped products.

---

## 12. Step 8: Run the Flask Service (Optional)

The Flask service is a small second API. It can process text and proxy requests to the FastAPI backend.

Open a **new terminal** (keep FastAPI running in the first one). Then:

```bash
cd ~/Desktop/backend
source venv/bin/activate
python flask_service/app.py
```

Flask will run on port 5000. Open:

```text
http://localhost:5000
```

You will see a list of Flask endpoints. For example, **POST /process/text** lets you send text and get back word count and character count.

---

## 13. Understanding the Two Scraping Tools

### BeautifulSoup (Static HTML)

**Use when:** The website’s content is already in the HTML when the page loads. No JavaScript needed to show the data.

- **Pros:** Fast, simple, no browser required
- **Cons:** Cannot handle content loaded by JavaScript

**Example:** books.toscrape.com, many news sites, simple product pages

### Selenium (Dynamic Pages)

**Use when:** The website uses JavaScript to load content. The data appears only after the page runs scripts.

- **Pros:** Can handle complex, dynamic sites
- **Cons:** Slower, needs Chrome/Chromium and chromedriver

**Example:** Single-page apps (SPAs), sites that load data via AJAX

---

## 14. Why Two Databases? (PostgreSQL and MongoDB)

### PostgreSQL (Relational)

- **Structured data** with a fixed schema (columns: title, price, etc.)
- **ACID** – reliable transactions
- **Good for:** Products, users, orders – anything with a clear structure

### MongoDB (Document Store)

- **Flexible data** – each document can have different fields
- **Good for:** Logs, raw scrape results, data that changes shape
- **No fixed schema** – you can add new fields anytime

In this project:

- **PostgreSQL** stores products (title, price, description)
- **MongoDB** stores scrape logs (source URL, count, raw product list)

---

## 15. Main API Endpoints (Quick Reference)

### FastAPI (Port 8000)

| Method | Endpoint | What It Does |
| ------ | -------- | ------------ |
| GET | `/` | Welcome message |
| GET | `/health` | Health check |
| GET | `/products` | List all products |
| GET | `/products/{id}` | Get one product |
| POST | `/products` | Create a product |
| PUT | `/products/{id}` | Update a product |
| DELETE | `/products/{id}` | Delete a product |
| POST | `/scrape` | Run the scraping pipeline |
| GET | `/external/posts` | Fetch posts from JSONPlaceholder |
| GET | `/scrape-logs` | View MongoDB scrape logs |

### Flask (Port 5000)

| Method | Endpoint | What It Does |
| ------ | -------- | ------------ |
| GET | `/` | Welcome message |
| GET | `/health` | Health check |
| POST | `/process/text` | Process text (word count, etc.) |
| GET | `/proxy/products` | Get products from FastAPI |
| GET | `/proxy/external-posts` | Get external posts via FastAPI |

---

## 16. Example: Create a Product with curl

From the terminal:

```bash
curl -X POST http://localhost:8000/products \
  -H "Content-Type: application/json" \
  -d '{"title":"My Product","price":19.99,"description":"A test item"}'
```

You will get back the created product with an ID. Then list all products:

```bash
curl http://localhost:8000/products
```

---

## 17. Example: Trigger a Scrape with a Custom URL

You can scrape any URL. Send a POST request:

```bash
curl -X POST http://localhost:8000/scrape \
  -H "Content-Type: application/json" \
  -d '{"url":"https://example.com","use_selenium":false}'
```

- **url:** The website to scrape
- **use_selenium:** `true` for JavaScript-heavy sites, `false` for static HTML (BeautifulSoup)

---

## 18. Useful Commands (Summary)

| Task | Command |
| ---- | ------- |
| Start databases | `docker-compose up -d` |
| Stop databases | `docker-compose down` |
| Create tables | `python -m db.init_db` |
| Run FastAPI | `uvicorn app.main:app --reload --port 8000` |
| Run Flask | `python flask_service/app.py` |
| API docs | Open http://localhost:8000/docs |

---

## 19. What You Learned (Summary)

You now know how to:

- Build **RESTful APIs** with FastAPI and Flask
- **Scrape websites** with BeautifulSoup (static) and Selenium (dynamic)
- Use **PostgreSQL** for structured data and **MongoDB** for flexible logs
- Run **PostgreSQL and MongoDB** with Docker
- Perform **CRUD operations** (Create, Read, Update, Delete)
- **Integrate with external APIs** (e.g. JSONPlaceholder)
- Push the project to **GitHub**

This project demonstrates skills I wanted on my resume: REST design, web scraping, picking the right database, and wiring up external APIs.
