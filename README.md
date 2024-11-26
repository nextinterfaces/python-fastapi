# Flask Example Project

This project is a Python-based web application leveraging Flask, SQLAlchemy, and related technologies to manage and interact with data.

## Features

- Database integration using SQLAlchemy ORM.
- Flask framework for routing and server management.
- Modular project structure for easy extensibility.

---

## Project Structure

```plaintext
.venv/                 # Virtual environment directory
auth/                  # Sample module
static/                # Static assets (CSS, JS, images)
templates/             # HTML templates for the front-end

app-main.py            # Main entry point for the Flask application
app-orm.py             # Handles ORM logic with SQLAlchemy
app-sql.py             # Direct SQL-based queries and database interactions
models.py              # Database models defined with SQLAlchemy
requirements.txt       # List of dependencies
```

---

## Installation

Follow these steps to set up and run the project locally:


Solution 2: Install virtualenv via pipx

If you specifically want to use virtualenv, you can use pipx, which is Homebrew’s recommended way to manage Python applications.

Install pipx if this is brew managed python:

    brew install pipx

Ensure pipx is configured:

    pipx ensurepath

Install virtualenv using pipx:

    pipx install virtualenv

Create a virtual environment using virtualenv:

    virtualenv .venv

Activate the virtual environment:

    source .venv/bin/activate

    # or deactivate when done
    deactivate

Proceed to install packages (e.g., Flask):

    pip install -r requirements.txt
    pip list

Or install manually:

    pip install fastapi
    pip install uvicorn
    pip freeze > requirements.txt
    pip list

Run the service:

    uvicorn main:app --reload

Based on this video:
https://www.youtube.com/watch?v=iWS9ogMPOI0
and repo:
https://github.com/pixegami/simple-fastapi-example

Use **pydantic** or **typeguard** for strict runtime checking
https://www.youtube.com/watch?v=XIdQ6gO3Anc

OpenAPI docs:
http://127.0.0.1:8000/redoc#
http://127.0.0.1:8000/docs#/




# FastAPI Concepts

FastAPI is a modern, fast (high-performance) web framework for building APIs with Python. It’s designed to be easy to use while leveraging Python type hints for automatic data validation and documentation generation.

---

## 1. Installation
```bash
pip install fastapi uvicorn
```
- `fastapi`: The core framework.
- `uvicorn`: A fast ASGI server to run FastAPI apps.

---

## 2. Creating a Basic API
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"Hello": "World"}
```
- `@app.get("/")`: Defines an HTTP GET endpoint at the root URL (`/`).
- The `read_root` function processes the request and returns a JSON response.

Run the app with:
```bash
uvicorn main:app --reload
```

---

## 3. Path Parameters
```python
@app.get("/items/{item_id}")
def read_item(item_id: int):
    return {"item_id": item_id}
```
- Path parameters are captured dynamically.
- FastAPI validates `item_id` as an `int` because of type hints.

---

## 4. Query Parameters
```python
@app.get("/items/")
def read_item(skip: int = 0, limit: int = 10):
    return {"skip": skip, "limit": limit}
```
- Query parameters (e.g., `?skip=0&limit=10`) are optional and can have default values.

---

## 5. Request Body
Use Pydantic models for data validation and serialization:
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float
    is_offer: bool = None

@app.post("/items/")
def create_item(item: Item):
    return {"item": item}
```
- FastAPI validates the input automatically based on the Pydantic model.

---

## 6. Dependency Injection
```python
from fastapi import Depends

def common_parameters(q: str = None, limit: int = 10):
    return {"q": q, "limit": limit}

@app.get("/items/")
def read_items(commons: dict = Depends(common_parameters)):
    return commons
```
- `Depends` lets you inject reusable logic into multiple endpoints.

---

## 7. Automatic Documentation
FastAPI automatically generates OpenAPI documentation:
- Swagger UI: Visit `/docs`
- ReDoc: Visit `/redoc`

---

## 8. Error Handling
```python
from fastapi import HTTPException

@app.get("/items/{item_id}")
def read_item(item_id: int):
    if item_id > 100:
        raise HTTPException(status_code=404, detail="Item not found")
    return {"item_id": item_id}
```

---

## 9. Middleware
Add middleware for processing requests and responses globally:
```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

---

## 10. Background Tasks
Run background jobs after sending a response:
```python
from fastapi import BackgroundTasks

def write_log(message: str):
    with open("log.txt", "a") as f:
        f.write(message)

@app.post("/send/")
def send_notification(background_tasks: BackgroundTasks, email: str):
    background_tasks.add_task(write_log, f"Notification sent to {email}\n")
    return {"message": "Notification sent"}
```

---

## 11. Asynchronous Endpoints
```python
import asyncio

@app.get("/async/")
async def read_async():
    await asyncio.sleep(1)
    return {"message": "Done"}
```

---

## 12. Security (OAuth2)
Use FastAPI’s built-in utilities for OAuth2:
```python
from fastapi.security import OAuth2PasswordBearer

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

@app.get("/users/me/")
def read_users_me(token: str = Depends(oauth2_scheme)):
    return {"token": token}
```
