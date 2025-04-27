# Learning Bottle by Building a Mini Blog

Bottle is a simple, lightweight Python web framework that's perfect for small projects like a mini blog. Let's walk through building one to learn the basics of Bottle.

## Setup

First, install Bottle:
```bash
pip install bottle
```

## Basic Structure

Create a file called `mini_blog.py`:

```python
from bottle import Bottle, run, template, request, redirect, static_file
import os
import sqlite3

app = Bottle()

# Database setup
DB_NAME = 'mini_blog.db'

def setup_database():
    conn = sqlite3.connect(DB_NAME)
    c = conn.cursor()
    c.execute('''CREATE TABLE IF NOT EXISTS posts 
                 (id INTEGER PRIMARY KEY AUTOINCREMENT, 
                  title TEXT, 
                  content TEXT,
                  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP)''')
    conn.commit()
    conn.close()

setup_database()
```

## Routes

### Home Page (List all posts)

```python
@app.route('/')
def home():
    conn = sqlite3.connect(DB_NAME)
    c = conn.cursor()
    c.execute("SELECT id, title, content, created_at FROM posts ORDER BY created_at DESC")
    posts = c.fetchall()
    conn.close()
    return template('home', posts=posts)
```

### View Single Post

```python
@app.route('/post/<post_id:int>')
def view_post(post_id):
    conn = sqlite3.connect(DB_NAME)
    c = conn.cursor()
    c.execute("SELECT id, title, content, created_at FROM posts WHERE id=?", (post_id,))
    post = c.fetchone()
    conn.close()
    if not post:
        return "Post not found"
    return template('post', post=post)
```

### Create New Post

```python
@app.route('/new', method=['GET', 'POST'])
def new_post():
    if request.method == 'POST':
        title = request.forms.get('title')
        content = request.forms.get('content')
        conn = sqlite3.connect(DB_NAME)
        c = conn.cursor()
        c.execute("INSERT INTO posts (title, content) VALUES (?, ?)", (title, content))
        conn.commit()
        conn.close()
        return redirect('/')
    return template('new_post')
```

### Static Files (CSS)

```python
@app.route('/static/<filename:path>')
def serve_static(filename):
    return static_file(filename, root='./static')
```

## Templates

Create a `views` folder and add these templates:

### home.tpl

```html
%# Template for the home page
% rebase('base', title='Mini Blog - Home')

<h1>Mini Blog</h1>
<a href="/new">Create New Post</a>

% for post in posts:
    <div class="post">
        <h2><a href="/post/{{post[0]}}">{{post[1]}}</a></h2>
        <p>{{post[3]}}</p>
        <p>{{!post[2][:100]}}...</p>
    </div>
% end
```

### post.tpl

```html
%# Template for viewing a single post
% rebase('base', title=post[1])

<h1>{{post[1]}}</h1>
<p><em>{{post[3]}}</em></p>
<hr>
<div>{{!post[2]}}</div>
<a href="/">Back to Home</a>
```

### new_post.tpl

```html
%# Template for creating a new post
% rebase('base', title='Create New Post')

<h1>Create New Post</h1>
<form action="/new" method="post">
    <label for="title">Title:</label>
    <input type="text" name="title" id="title" required>
    <br>
    <label for="content">Content:</label>
    <textarea name="content" id="content" required></textarea>
    <br>
    <input type="submit" value="Create Post">
</form>
```

### base.tpl

```html
<!DOCTYPE html>
<html>
<head>
    <title>{{title}}</title>
    <link rel="stylesheet" href="/static/style.css">
</head>
<body>
    {{!base}}
</body>
</html>
```

## CSS

Create a `static` folder and add `style.css`:

```css
body {
    font-family: Arial, sans-serif;
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
}

.post {
    margin-bottom: 30px;
    padding: 15px;
    border: 1px solid #ddd;
    border-radius: 5px;
}

form label {
    display: block;
    margin-top: 10px;
}

form input[type="text"],
form textarea {
    width: 100%;
    padding: 8px;
    margin-top: 5px;
}

form textarea {
    height: 200px;
}

form input[type="submit"] {
    margin-top: 15px;
    padding: 8px 15px;
}
```

## Running the Application

Add this to the end of `mini_blog.py`:

```python
if __name__ == '__main__':
    run(app, host='localhost', port=8080, debug=True, reloader=True)
```

Then run:
```bash
python mini_blog.py
```

Visit `http://localhost:8080` in your browser to see your mini blog!

## Features Implemented

1. Simple routing with Bottle
2. SQLite database integration
3. Template rendering
4. Static file serving
5. CRUD operations (Create, Read - still needs Update, Delete)
6. Basic styling

## Next Steps to Enhance

- Add edit/delete functionality
- Implement user authentication
- Add comments
- Add pagination
- Improve error handling
- Add markdown support for posts

This mini blog covers the basics of Bottle while providing a functional application you can expand upon.
