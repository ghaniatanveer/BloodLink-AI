# 🩸 BloodLink-AI

> A role-based blood donation management and intelligent assistance platform that connects donors and hospitals through city-wise discovery, blood-group compatibility logic, and an AI-powered chatbot.

![Node.js](https://img.shields.io/badge/Node.js-18.x-green)
![Express](https://img.shields.io/badge/Express-4.x-blue)
![SQLite](https://img.shields.io/badge/SQLite-3.x-lightblue)
![JWT](https://img.shields.io/badge/JWT-Auth-orange)
![License](https://img.shields.io/badge/License-MIT-yellow)

---

## 📌 Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [System Workflow](#system-workflow)
- [Installation & Setup](#installation--setup)
- [Environment Variables](#environment-variables)
- [API Endpoints](#api-endpoints)
- [AI Assistant](#ai-assistant)
- [Database Schema](#database-schema)
- [Project Structure](#project-structure)
- [Limitations & Future Work](#limitations--future-work)
- [License](#license)

---

## 📖 Overview

BloodLink-AI is a full-stack web application built to streamline emergency blood donation coordination. It implements **role-based access control** (donor/hospital), **city-wise request filtering**, **blood-group compatibility validation**, and a complete request lifecycle (create → accept → approve → complete). The integrated AI chatbot provides donation guidance using Gemini/Groq APIs with a graceful fallback mechanism when external LLMs are unavailable.

---

## ✨ Features

### 👤 Donor Features
- Register/login as a donor with blood group & city
- View nearby open blood requests (compatible blood group + same city)
- Accept a request and wait for hospital approval
- Track donation history (accepted, approved, completed)

### 🏥 Hospital Features
- Register/login as a hospital
- Create blood requests (blood group, units, urgency, location)
- View list of donors who accepted their requests
- Approve or reject donor candidates
- Mark requests as completed
- Access completed donation history

### 🤖 AI Assistant
- Command shortcuts (`find donors`, `open requests`, etc.) – deterministic DB responses
- General Q&A using Gemini API (with fallback to Groq)
- Offline educational responses when API quota is exhausted
- Conversation memory (in‑process, not persisted)

### 🔒 Security
- JWT authentication & role-based middleware
- bcrypt password hashing
- In‑memory rate limiting (60 req/min per IP)
- Input validation for all endpoints

### 📁 Other
- Public directory search (donors & hospitals)
- City & blood-group matching logic
- SQLite – zero config, local deployment ready

---

## 🛠️ Tech Stack

| Layer       | Technology |
|-------------|------------|
| Runtime     | Node.js (ES Modules) |
| Framework   | Express.js |
| Database    | SQLite (better-sqlite3) |
| Auth        | JWT + bcryptjs |
| AI APIs     | Google Gemini, Groq (with fallback) |
| Environment | dotenv |
| Dev Tool    | nodemon |
| Frontend    | Vanilla HTML, CSS, JavaScript |

---

## 🔄 System Workflow

- **Compatibility check** – donor blood group must be compatible with request’s group (using `canDonateTo` logic)
- **City constraint** – donor and request city must match (distance computed if coordinates available)
- **Request statuses** – `open` → `in_progress` (after donor acceptance) → `completed` (hospital marks done)

---

## 🚀 Installation & Setup

### Prerequisites
- Node.js (v18 or higher)
- npm or yarn
- SQLite (included via better-sqlite3)

### Steps

```bash
# Clone the repository
git clone https://github.com/GitwithHaseeb/BloodLink-AI.git
cd BloodLink-AI

# Install dependencies
npm install

# Create .env file (see below)
# Populate with your API keys and JWT secret

# Seed the database (donor/hospital data)
npm run seed   # if you have a seeding script, otherwise it seeds on first run

# Start the server
npm startPORT=3000
JWT_SECRET=your_super_secret_key_change_this

# AI API keys (optional – without them chatbot uses fallback responses)
GEMINI_API_KEY=your_google_gemini_api_key
GROQ_API_KEY=your_groq_api_key

🤖 AI Assistant Details
Command mode – find donors in <city>, open requests in <city>, how to donate → direct DB responses, no API cost.

Generative mode – uses Gemini (preferred) or Groq (fallback). If both fail or quota exceeded, returns a friendly educational response.

Fallback chain – Gemini → Groq → Hardcoded donation FAQ.

Conversation trimming – prevents token overflow.
🗄️ Database Schema (SQLite)
Main tables:

users – id, name, email, password_hash, role, city, blood_group (donors), coordinates, contact

requests – id, hospital_id, blood_group, units, urgency, status, city, created_at

request_candidates – id, request_id, donor_id, status (pending/approved/rejected), accepted_at

meta – version/seeding flags

Indexes on city, status, blood_group, and foreign keys ensure query performance.

📁 Project Structure
text
BloodLink-AI/
├── .env.example
├── .gitignore
├── package.json
├── app.js                   # Express app entry
├── server.js               # Server start (if separate)
├── config/                 # DB config, env loader
├── controllers/            # Business logic for auth, requests, chat, directory
├── db/                     # SQLite connection and schema initialization
├── middleware/             # Auth, rate limiter, error handler
├── routes/                 # Express route definitions
├── public/                 # Static frontend (HTML, CSS, JS)
├── utils/                  # Blood compatibility, AI fallback, helpers
├── data/                   # SQLite database files (ignored by git if .db)
└── README.md
⚠️ Limitations & Future Work
Rate limiter – in‑memory only; not suitable for distributed deployment.

Conversations – not persisted across server restarts (in‑process only).

No automated test suite – only manual endpoint testing.

Notifications – SMS/WhatsApp integration planned.

Geospatial indexing – currently distance is computed ad‑hoc.

Production hardening – HTTPS, audit logs, secret rotation.

📄 License
This project is licensed under the MIT License – see the LICENSE file for details.



Acknowledgements
Express.js, SQLite, JWT communities

Google Gemini & Groq for AI APIs

IEEE format guidelines for the accompanying report
