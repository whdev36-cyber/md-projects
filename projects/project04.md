# Learning Flask by Building a Twitter Clone

This guide will walk you through building a Twitter-like application using Flask, covering authentication, user profiles, and CRUD (Create, Read, Update, Delete) operations.

## Project Setup

First, let's set up our project structure:

```
twitter_clone/
├── app/
│   ├── __init__.py
│   ├── models.py
│   ├── routes.py
│   ├── forms.py
│   ├── templates/
│   │   ├── base.html
│   │   ├── index.html
│   │   ├── login.html
│   │   ├── register.html
│   │   ├── profile.html
│   │   ├── post.html
│   │   └── feed.html
│   └── static/
│       └── style.css
├── config.py
└── run.py
```

## 1. Basic Flask Setup

First, install Flask and required extensions:

```bash
pip install flask flask-sqlalchemy flask-login flask-wtf email-validator
```

Create `app/__init__.py`:

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_login import LoginManager

db = SQLAlchemy()
login_manager = LoginManager()

def create_app():
    app = Flask(__name__)
    app.config.from_object('config.Config')
    
    db.init_app(app)
    login_manager.init_app(app)
    login_manager.login_view = 'login'
    
    with app.app_context():
        from . import routes
        db.create_all()
        
    return app
```

Create `config.py`:

```python
import os

class Config:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'your-secret-key-here'
    SQLALCHEMY_DATABASE_URI = 'sqlite:///twitter_clone.db'
    SQLALCHEMY_TRACK_MODIFICATIONS = False
```

## 2. Authentication System

### Models (`app/models.py`)

```python
from app import db, login_manager
from flask_login import UserMixin
from datetime import datetime

@login_manager.user_loader
def load_user(user_id):
    return User.query.get(int(user_id))

class User(db.Model, UserMixin):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(20), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    image_file = db.Column(db.String(20), nullable=False, default='default.jpg')
    password = db.Column(db.String(60), nullable=False)
    posts = db.relationship('Post', backref='author', lazy=True)
    
    def __repr__(self):
        return f"User('{self.username}', '{self.email}')"

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    content = db.Column(db.String(280), nullable=False)
    date_posted = db.Column(db.DateTime, nullable=False, default=datetime.utcnow)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    
    def __repr__(self):
        return f"Post('{self.content}', '{self.date_posted}')"
```

### Forms (`app/forms.py`)

```python
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, SubmitField, TextAreaField
from wtforms.validators import DataRequired, Length, Email, EqualTo, ValidationError
from app.models import User

class RegistrationForm(FlaskForm):
    username = StringField('Username', validators=[DataRequired(), Length(min=2, max=20)])
    email = StringField('Email', validators=[DataRequired(), Email()])
    password = PasswordField('Password', validators=[DataRequired()])
    confirm_password = PasswordField('Confirm Password', 
                                    validators=[DataRequired(), EqualTo('password')])
    submit = SubmitField('Sign Up')
    
    def validate_username(self, username):
        user = User.query.filter_by(username=username.data).first()
        if user:
            raise ValidationError('That username is taken. Please choose a different one.')
    
    def validate_email(self, email):
        user = User.query.filter_by(email=email.data).first()
        if user:
            raise ValidationError('That email is taken. Please choose a different one.')

class LoginForm(FlaskForm):
    email = StringField('Email', validators=[DataRequired(), Email()])
    password = PasswordField('Password', validators=[DataRequired()])
    submit = SubmitField('Login')

class PostForm(FlaskForm):
    content = TextAreaField('Tweet', validators=[DataRequired(), Length(max=280)])
    submit = SubmitField('Post')
```

### Routes (`app/routes.py`)

```python
from flask import render_template, url_for, flash, redirect, request
from app import app, db, bcrypt
from app.forms import RegistrationForm, LoginForm, PostForm
from app.models import User, Post
from flask_login import login_user, current_user, logout_user, login_required

@app.route('/')
@app.route('/home')
def home():
    posts = Post.query.order_by(Post.date_posted.desc()).all()
    return render_template('feed.html', posts=posts)

@app.route('/register', methods=['GET', 'POST'])
def register():
    if current_user.is_authenticated:
        return redirect(url_for('home'))
    form = RegistrationForm()
    if form.validate_on_submit():
        hashed_password = bcrypt.generate_password_hash(form.password.data).decode('utf-8')
        user = User(username=form.username.data, email=form.email.data, password=hashed_password)
        db.session.add(user)
        db.session.commit()
        flash('Your account has been created! You are now able to log in', 'success')
        return redirect(url_for('login'))
    return render_template('register.html', title='Register', form=form)

@app.route('/login', methods=['GET', 'POST'])
def login():
    if current_user.is_authenticated:
        return redirect(url_for('home'))
    form = LoginForm()
    if form.validate_on_submit():
        user = User.query.filter_by(email=form.email.data).first()
        if user and bcrypt.check_password_hash(user.password, form.password.data):
            login_user(user, remember=form.remember.data)
            next_page = request.args.get('next')
            return redirect(next_page) if next_page else redirect(url_for('home'))
        else:
            flash('Login Unsuccessful. Please check email and password', 'danger')
    return render_template('login.html', title='Login', form=form)

@app.route('/logout')
def logout():
    logout_user()
    return redirect(url_for('home'))

@app.route('/profile')
@login_required
def profile():
    posts = current_user.posts
    return render_template('profile.html', posts=posts)

@app.route('/post/new', methods=['GET', 'POST'])
@login_required
def new_post():
    form = PostForm()
    if form.validate_on_submit():
        post = Post(content=form.content.data, author=current_user)
        db.session.add(post)
        db.session.commit()
        flash('Your post has been created!', 'success')
        return redirect(url_for('home'))
    return render_template('create_post.html', title='New Post', form=form)
```

## 3. Templates

### Base Template (`app/templates/base.html`)

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>{% block title %}Twitter Clone{% endblock %}</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
    <nav>
        <div class="container">
            <a href="{{ url_for('home') }}">Twitter Clone</a>
            <div class="nav-links">
                {% if current_user.is_authenticated %}
                    <a href="{{ url_for('profile') }}">Profile</a>
                    <a href="{{ url_for('new_post') }}">New Post</a>
                    <a href="{{ url_for('logout') }}">Logout</a>
                {% else %}
                    <a href="{{ url_for('login') }}">Login</a>
                    <a href="{{ url_for('register') }}">Register</a>
                {% endif %}
            </div>
        </div>
    </nav>
    <div class="container">
        {% with messages = get_flashed_messages(with_categories=true) %}
            {% if messages %}
                {% for category, message in messages %}
                    <div class="alert alert-{{ category }}">
                        {{ message }}
                    </div>
                {% endfor %}
            {% endif %}
        {% endwith %}
        {% block content %}{% endblock %}
    </div>
</body>
</html>
```

### Registration Template (`app/templates/register.html`)

```html
{% extends "base.html" %}
{% block title %}Register{% endblock %}
{% block content %}
    <h1>Register</h1>
    <form method="POST" action="">
        {{ form.hidden_tag() }}
        <div>
            {{ form.username.label }}
            {{ form.username }}
            {% for error in form.username.errors %}
                <span>{{ error }}</span>
            {% endfor %}
        </div>
        <div>
            {{ form.email.label }}
            {{ form.email }}
            {% for error in form.email.errors %}
                <span>{{ error }}</span>
            {% endfor %}
        </div>
        <div>
            {{ form.password.label }}
            {{ form.password }}
            {% for error in form.password.errors %}
                <span>{{ error }}</span>
            {% endfor %}
        </div>
        <div>
            {{ form.confirm_password.label }}
            {{ form.confirm_password }}
            {% for error in form.confirm_password.errors %}
                <span>{{ error }}</span>
            {% endfor %}
        </div>
        <div>
            {{ form.submit }}
        </div>
    </form>
    <div>
        Already have an account? <a href="{{ url_for('login') }}">Login</a>
    </div>
{% endblock %}
```

### Login Template (`app/templates/login.html`)

```html
{% extends "base.html" %}
{% block title %}Login{% endblock %}
{% block content %}
    <h1>Login</h1>
    <form method="POST" action="">
        {{ form.hidden_tag() }}
        <div>
            {{ form.email.label }}
            {{ form.email }}
            {% for error in form.email.errors %}
                <span>{{ error }}</span>
            {% endfor %}
        </div>
        <div>
            {{ form.password.label }}
            {{ form.password }}
            {% for error in form.password.errors %}
                <span>{{ error }}</span>
            {% endfor %}
        </div>
        <div>
            {{ form.submit }}
        </div>
    </form>
    <div>
        Don't have an account? <a href="{{ url_for('register') }}">Register</a>
    </div>
{% endblock %}
```

### Profile Template (`app/templates/profile.html`)

```html
{% extends "base.html" %}
{% block title %}Profile{% endblock %}
{% block content %}
    <h1>{{ current_user.username }}'s Profile</h1>
    <img src="{{ url_for('static', filename='profile_pics/' + current_user.image_file) }}">
    <h2>Your Posts</h2>
    {% for post in posts %}
        <div class="post">
            <p>{{ post.content }}</p>
            <small>Posted on {{ post.date_posted.strftime('%Y-%m-%d') }}</small>
        </div>
    {% endfor %}
{% endblock %}
```

### Feed Template (`app/templates/feed.html`)

```html
{% extends "base.html" %}
{% block title %}Home{% endblock %}
{% block content %}
    <h1>Twitter Clone Feed</h1>
    {% for post in posts %}
        <div class="post">
            <img src="{{ url_for('static', filename='profile_pics/' + post.author.image_file) }}">
            <a href="#">{{ post.author.username }}</a>
            <small>{{ post.date_posted.strftime('%Y-%m-%d') }}</small>
            <p>{{ post.content }}</p>
        </div>
    {% endfor %}
{% endblock %}
```

### Post Creation Template (`app/templates/create_post.html`)

```html
{% extends "base.html" %}
{% block title %}New Post{% endblock %}
{% block content %}
    <h1>Create New Post</h1>
    <form method="POST" action="">
        {{ form.hidden_tag() }}
        <div>
            {{ form.content.label }}
            {{ form.content }}
            {% for error in form.content.errors %}
                <span>{{ error }}</span>
            {% endfor %}
        </div>
        <div>
            {{ form.submit }}
        </div>
    </form>
{% endblock %}
```

## 4. Run the Application

Create `run.py`:

```python
from app import create_app

app = create_app()

if __name__ == '__main__':
    app.run(debug=True)
```

Run the application:

```bash
python run.py
```

## 5. Additional Features to Implement

1. **Follow/Unfollow System**:
   - Add a many-to-many relationship for followers
   - Create routes to follow/unfollow users

2. **Post Management**:
   - Add edit and delete functionality for posts
   - Implement likes and retweets

3. **Enhanced Profile**:
   - Allow profile picture uploads
   - Add bio and location fields

4. **Notifications**:
   - Implement a simple notification system

5. **Search Functionality**:
   - Add search for users and posts

This Twitter clone covers all the fundamentals of Flask including authentication, user profiles, and CRUD operations. You can now expand it with additional features to make it more robust!
