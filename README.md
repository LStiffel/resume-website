# My Portfolio — FastAPI Web Application

A full-stack web application built with **FastAPI** and **SQLite**, featuring user authentication, profile management, and a clean Jinja2-templated frontend.

---

## Table of Contents

- [Overview](#overview)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [API Reference](#api-reference)
- [Authentication](#authentication)
- [Data Models](#data-models)

---

## Overview

This application provides a personal portfolio platform with a complete user management system. Users can sign up, log in via JWT-based authentication, view their profile, and update their display name — all through a minimal, responsive web interface.

---

## Tech Stack

| Layer        | Technology                        |
|--------------|-----------------------------------|
| Backend      | FastAPI                           |
| Database     | SQLite via SQLAlchemy ORM         |
| Auth         | JWT (python-jose) + bcrypt        |
| Templating   | Jinja2                            |
| Validation   | Pydantic v2                       |
| Server       | Uvicorn                           |

---

## Project Structure

```
.
├── main.py                  # Application entry point
├── data/
│   └── database.db          # SQLite database (auto-created)
├── app/
│   ├── database.py          # SQLAlchemy engine & session
│   ├── models.py            # ORM model (UserORM)
│   ├── schemas.py           # Pydantic schemas
│   ├── crud.py              # Database operations
│   ├── auth.py              # JWT & password utilities
│   └── routers/
│       ├── users.py         # User API endpoints
│       └── pages.py         # HTML page routes
└── templates/
    ├── base.html            # Shared layout
    ├── home.html            # Landing page
    ├── signup.html          # Registration form
    ├── login.html           # Login form
    ├── profile.html         # User profile view
    └── modify_name.html     # Name update form
```

---

## Getting Started

### Prerequisites

- Python 3.10+
- pip

### Installation

```bash
# Clone the repository
git clone https://github.com/LStiffel/resume-website.git
cd resume-website

# Install dependencies
pip install -r requirements.txt

# Run the application
python main.py 
or
py main.py
or 
python3 main.py
```

The app will be available at **http://localhost:8000**.

### Pages

| Route          | Description              |
|----------------|--------------------------|
| `/`            | Home / landing page      |
| `/sign_up`     | User registration        |
| `/login`       | User login               |
| `/profile`     | Authenticated user profile |
| `/modify_name` | Update display name      |

---

## API Reference

### Auth

| Method | Endpoint | Description                        |
|--------|----------|------------------------------------|
| `POST` | `/token` | Login — returns a JWT access token |

**Request body** (`application/x-www-form-urlencoded`):
```
username=user@example.com&password=secret
```

**Response:**
```json
{
  "access_token": "<jwt>",
  "token_type": "bearer"
}
```

---

### Users

| Method   | Endpoint             | Auth required | Description              |
|----------|----------------------|---------------|--------------------------|
| `POST`   | `/add_user`          | No            | Register a new user      |
| `GET`    | `/me`                | Yes           | Get current user profile |
| `GET`    | `/user_database`     | No            | List all users           |
| `POST`   | `/modify_user_name`  | Yes           | Update display name      |
| `DELETE` | `/delete_user`       | No            | Delete a user by ID      |

#### `POST /add_user`

```json
{
  "password": "secret",
  "info": {
    "name": "Jane Doe",
    "coordinate": {
      "email": "jane@example.com"
    }
  }
}
```

#### `POST /modify_user_name`

Query params: `?name=NewName`

Requires `Authorization: Bearer <token>` header.

---

## Authentication

Authentication is handled with **JWT tokens** (HS256). After logging in via `/token`, the client stores the token in `localStorage` and sends it as a `Bearer` token on protected requests.

- Token expiry: **60 minutes**
- User identity is derived from the JWT `sub` claim, which holds the user's email address

> ⚠️ **Note:** The `SECRET_KEY` in `auth.py` is a placeholder. Replace it with a long, random string before deploying to production.

---

## Data Models

### User (stored in SQLite as JSON columns)

```
id            → user's email (primary key)
password      → bcrypt hash
info          → PersonalInfo JSON object
experiences   → list of Experience objects (optional)
formations    → list of Formation objects (optional)
skills        → list of Skill objects (optional)
```

### PersonalInfo

```
name          → string (required)
birth_date    → string (optional)
home          → string (optional)
coordinate    → { email, tel? }
interest      → [{ name, description }] (optional)
```