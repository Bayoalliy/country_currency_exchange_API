# 🌍 Country Currency & Exchange API

A RESTful API that fetches, processes, and caches global country data along with live currency exchange rates. It provides CRUD operations, computed GDP estimates, and generates a summary image of top economies.

---

## 🧩 Overview

This project integrates two public APIs to deliver real-time country and currency data:

- **Countries API:** [`https://restcountries.com/v2/all?fields=name,capital,region,population,flag,currencies`](https://restcountries.com/v2/all?fields=name,capital,region,population,flag,currencies)
- **Exchange Rate API:** [`https://open.er-api.com/v6/latest/USD`](https://open.er-api.com/v6/latest/USD)

It then:
1. Extracts and matches currency codes to exchange rates.  
2. Calculates a pseudo GDP estimate using population and exchange rate.  
3. Caches everything in a **MySQL database** for efficient querying.  
4. Generates a visual summary image (`cache/summary.png`) of top economies.

---

## 🚀 Features

| Feature | Description |
|----------|--------------|
| **Data Fetching** | Pulls all countries and live USD-based exchange rates. |
| **Data Processing** | Computes estimated GDP = `population × random(1000–2000) ÷ exchange_rate`. |
| **Database Caching** | Stores or updates all country data in MySQL for fast access. |
| **Filtering & Sorting** | Filter by region or currency; sort by GDP ascending/descending. |
| **Status Tracking** | Tracks total countries and last refresh timestamp. |
| **Image Generation** | Creates a PNG summary with top 5 economies and total count. |
| **CRUD Endpoints** | Retrieve, delete, or refresh countries data. |

---

## 🗄️ Database Schema

**Table:** `countries`

| Field | Type | Description |
|--------|------|-------------|
| id | INT (AUTO_INCREMENT) | Primary key |
| name | VARCHAR | Country name (unique, required) |
| capital | VARCHAR | Capital city |
| region | VARCHAR | Continent or region |
| population | BIGINT | Country population |
| currency_code | VARCHAR | e.g., NGN, USD |
| exchange_rate | FLOAT | Rate vs USD |
| estimated_gdp | FLOAT | Computed value |
| flag_url | TEXT | URL of country flag |
| last_refreshed_at | DATETIME | Timestamp of last update |

---

## ⚙️ Installation & Setup

### 1️⃣ Clone the Repository
```bash
git clone https://github.com/yourusername/country-currency-exchange-api.git
cd country-currency-exchange-api
```

### 2️⃣ Install Dependencies

```bash
npm install
```

### 3️⃣ Configure Environment Variables

Create a .env file in the root directory:

```bash
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=yourpassword
DB_NAME=country_exchange
PORT=5000
```

### 4️⃣ Initialize Database

```bash
npm run migrate
```

(or run SQL schema manually if provided)

### 5️⃣ Start the Server

```bash
npm start

Server runs on http://localhost:5000.
```

---

## 🧠 API Endpoints

### 🔁 POST /countries/refresh

Fetches all countries and exchange rates, computes estimated GDP, and caches everything in the database.

Responses:

✅ 200 OK → { "message": "Data refreshed successfully" }

❌ 503 Service Unavailable → { "error": "External data source unavailable", "details": "Could not fetch data from [API name]" }



---

### 🌐 GET /countries

Returns all cached countries.
Supports filters and sorting.

Query Parameters:

Parameter	Example	Description

region	?region=Africa	Filter by region
currency	?currency=NGN	Filter by currency
sort	?sort=gdp_desc	Sort by GDP ascending/descending



---

### 🔍 GET /countries/:name

Retrieve a single country record by name.

Example:

GET /countries/Nigeria


---

### 🗑️ DELETE /countries/:name

Deletes a specific country record from the database.


---

### 📊 GET /status

Returns system statistics.

Response Example:

{
  "total_countries": 250,
  "last_refreshed_at": "2025-10-22T18:00:00Z"
}


---

### 🖼️ GET /countries/image

Serves the cached summary image generated during refresh.

✅ Returns the PNG image file

❌ If missing: { "error": "Summary image not found" }



---

#### 💡 Computation Logic

For each country:

estimated_gdp = (population * random(1000, 2000)) / exchange_rate

If no currency_code: → exchange_rate = null, estimated_gdp = 0

If currency not found in exchange rate API: → exchange_rate = null, estimated_gdp = null


Random multiplier (1000–2000) is regenerated every refresh cycle.


---


### 🖼️ Image Generation Details

When /countries/refresh runs:

After DB update, generate a PNG summary:

Total number of countries

Top 5 by estimated GDP

Timestamp of refresh


Save to cache/summary.png


Used for /countries/image endpoint.


---

### 🧪 Example Response

```bash
GET /countries?region=Africa

[
  {
    "id": 1,
    "name": "Nigeria",
    "capital": "Abuja",
    "region": "Africa",
    "population": 206139589,
    "currency_code": "NGN",
    "exchange_rate": 1600.23,
    "estimated_gdp": 25767448125.2,
    "flag_url": "https://flagcdn.com/ng.svg",
    "last_refreshed_at": "2025-10-22T18:00:00Z"
  },
  {
    "id": 2,
    "name": "Ghana",
    "capital": "Accra",
    "region": "Africa",
    "population": 31072940,
    "currency_code": "GHS",
    "exchange_rate": 15.34,
    "estimated_gdp": 3029834520.6,
    "flag_url": "https://flagcdn.com/gh.svg",
    "last_refreshed_at": "2025-10-22T18:00:00Z"
  }
]
```

---

### 🧰 Tech Stack

Backend: Node.js (Express)

Database: MySQL

ORM: Sequelize / MySQL2 (depending on your setup)

Image Generation: node-html-to-image

Environment: Railway



---

### 🧾 Error Handling

Error	HTTP Code	Example

Missing fields	400	{ "error": "Validation failed" }
External API fail	503	{ "error": "External data source unavailable" }
Country not found	404	{ "error": "Country not found" }
Server error	500	{ "error": "Internal server error" }



---

🧑‍💻 Author

Adebayo Aliyu
Backend Engineer | Node.js Developer
💼 Building APIs and AI-integrated systems
📧 [adebayoalliy@gmail.com]
🌐 GitHub


---

#### 📜 License

MIT License © 2025 — Adebayo Aliyu

---

