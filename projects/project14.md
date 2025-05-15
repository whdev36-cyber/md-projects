# Learn FastAPI with One Project: Build a Todo API

FastAPI is a modern, fast (high-performance) web framework for building APIs with Python. Here's a comprehensive project that will teach you FastAPI's core concepts through building a Todo API.

## Project Setup

1. **Install FastAPI and Uvicorn** (ASGI server):
```bash
pip install fastapi uvicorn
```

## Step 1: Basic FastAPI App

Create `main.py`:
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Welcome to the Todo API"}
```

Run the server:
```bash
uvicorn main:app --reload
```

Visit `http://127.0.0.1:8000` to see your API working!

## Step 2: Add Todo Model and In-Memory Database

```python
from typing import List, Optional
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

# Todo Model
class Todo(BaseModel):
    id: int
    title: str
    description: Optional[str] = None
    completed: bool = False

# In-memory database
todos_db: List[Todo] = []
current_id = 0
```

## Step 3: CRUD Operations

### Create Todo
```python
@app.post("/todos/", response_model=Todo)
def create_todo(todo: Todo):
    global current_id
    current_id += 1
    todo.id = current_id
    todos_db.append(todo)
    return todo
```

### Read Todos
```python
@app.get("/todos/", response_model=List[Todo])
def read_todos():
    return todos_db

@app.get("/todos/{todo_id}", response_model=Todo)
def read_todo(todo_id: int):
    for todo in todos_db:
        if todo.id == todo_id:
            return todo
    raise HTTPException(status_code=404, detail="Todo not found")
```

### Update Todo
```python
@app.put("/todos/{todo_id}", response_model=Todo)
def update_todo(todo_id: int, updated_todo: Todo):
    for index, todo in enumerate(todos_db):
        if todo.id == todo_id:
            updated_todo.id = todo_id
            todos_db[index] = updated_todo
            return updated_todo
    raise HTTPException(status_code=404, detail="Todo not found")
```

### Delete Todo
```python
@app.delete("/todos/{todo_id}")
def delete_todo(todo_id: int):
    for index, todo in enumerate(todos_db):
        if todo.id == todo_id:
            todos_db.pop(index)
            return {"message": "Todo deleted successfully"}
    raise HTTPException(status_code=404, detail="Todo not found")
```

## Step 4: Add Search and Filtering

```python
@app.get("/todos/search/")
def search_todos(completed: Optional[bool] = None, title: Optional[str] = None):
    results = todos_db
    
    if completed is not None:
        results = [todo for todo in results if todo.completed == completed]
    
    if title:
        results = [todo for todo in results if title.lower() in todo.title.lower()]
    
    return results
```

## Step 5: Add Documentation

FastAPI automatically generates documentation:
- Swagger UI: `http://127.0.0.1:8000/docs`
- ReDoc: `http://127.0.0.1:8000/redoc`

## Step 6: Add Error Handling

```python
from fastapi import status

@app.post("/todos/", response_model=Todo, status_code=status.HTTP_201_CREATED)
def create_todo(todo: Todo):
    global current_id
    current_id += 1
    todo.id = current_id
    todos_db.append(todo)
    return todo
```

## Step 7: Add Async Support

```python
@app.get("/todos/async/", response_model=List[Todo])
async def read_todos_async():
    # Simulate async operation
    return todos_db
```

## Step 8: Deploy Your API

Deploy to Heroku:
1. Create `requirements.txt`:
```bash
pip freeze > requirements.txt
```

2. Create `Procfile`:
```
web: uvicorn main:app --host=0.0.0.0 --port=${PORT:-8000}
```

3. Push to Heroku and enjoy your live API!

## Next Steps

1. Add user authentication
2. Connect to a real database (SQLite, PostgreSQL)
3. Add tests
4. Implement rate limiting
5. Add OpenAPI documentation customization

This project covers all the fundamentals of FastAPI:
- Path operations
- Request bodies
- Response models
- Error handling
- Async support
- Automatic documentation

You've now built a complete CRUD API with FastAPI!