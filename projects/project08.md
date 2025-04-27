# Learning Python Pyramid by Building an Anime Website

Pyramid is a lightweight Python web framework that's great for building web applications of all sizes. In this guide, I'll walk you through building a simple anime website using Pyramid, covering all the key concepts along the way.

## Project Setup

First, let's set up our development environment:

```bash
# Create a virtual environment
python -m venv venv
source venv/bin/activate  # On Windows use: venv\Scripts\activate

# Install Pyramid
pip install pyramid
```

## Creating a Basic Pyramid Application

1. Create a new file `app.py` with the following content:

```python
from wsgiref.simple_server import make_server
from pyramid.config import Configurator
from pyramid.response import Response

def anime_list(request):
    return Response("""
        <h1>Anime List</h1>
        <ul>
            <li>Attack on Titan</li>
            <li>Demon Slayer</li>
            <li>My Hero Academia</li>
        </ul>
    """)

if __name__ == '__main__':
    with Configurator() as config:
        config.add_route('anime_list', '/')
        config.add_view(anime_list, route_name='anime_list')
        app = config.make_wsgi_app()
    server = make_server('0.0.0.0', 6543, app)
    server.serve_forever()
```

Run it with `python app.py` and visit http://localhost:6543 to see your basic anime list.

## Adding Templates with Chameleon

Pyramid supports multiple templating engines. Let's use Chameleon:

```bash
pip install pyramid_chameleon
```

Update `app.py`:

```python
from pyramid.config import Configurator
from pyramid.view import view_config

@view_config(route_name='anime_list', renderer='templates/anime_list.pt')
def anime_list(request):
    return {'animes': [
        {'title': 'Attack on Titan', 'episodes': 75},
        {'title': 'Demon Slayer', 'episodes': 44},
        {'title': 'My Hero Academia', 'episodes': 113}
    ]}

if __name__ == '__main__':
    with Configurator() as config:
        config.include('pyramid_chameleon')
        config.add_route('anime_list', '/')
        config.scan()
        app = config.make_wsgi_app()
    from wsgiref.simple_server import make_server
    server = make_server('0.0.0.0', 6543, app)
    server.serve_forever()
```

Create a `templates` directory and add `anime_list.pt`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Anime Website</title>
</head>
<body>
    <h1>Anime List</h1>
    <ul>
        <li tal:repeat="anime animes">
            ${anime.title} (${anime.episodes} episodes)
        </li>
    </ul>
</body>
</html>
```

## Adding Static Assets

Create a `static` directory for CSS, JS, and images:

```python
# Add this to your configuration
config.add_static_view(name='static', path='static')
```

Now you can reference static files in your templates:

```html
<link rel="stylesheet" href="${request.static_url('animewebsite:static/style.css')}">
```

## Database Integration with SQLAlchemy

Let's add a database to store our anime:

```bash
pip install pyramid_tm zope.sqlalchemy sqlalchemy
```

Create a `models.py`:

```python
from sqlalchemy import Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

class Anime(Base):
    __tablename__ = 'animes'
    id = Column(Integer, primary_key=True)
    title = Column(String(100), nullable=False)
    episodes = Column(Integer)
    genre = Column(String(50))
```

Update `app.py` to include database configuration:

```python
from pyramid.config import Configurator
from sqlalchemy import engine_from_config
from .models import Base

def main(global_config, **settings):
    engine = engine_from_config(settings, 'sqlalchemy.')
    Base.metadata.create_all(engine)
    
    with Configurator(settings=settings) as config:
        config.include('pyramid_tm')
        config.include('pyramid_chameleon')
        config.add_static_view('static', 'static')
        config.add_route('anime_list', '/')
        config.scan()
        return config.make_wsgi_app()
```

## Creating Views for CRUD Operations

Let's add views to create, read, update, and delete anime:

```python
from pyramid.view import view_config
from pyramid.httpexceptions import HTTPFound
from sqlalchemy.orm import sessionmaker
from .models import Anime

@view_config(route_name='anime_list', renderer='templates/anime_list.pt')
def anime_list(request):
    DBSession = request.dbsession
    animes = DBSession.query(Anime).all()
    return {'animes': animes}

@view_config(route_name='add_anime', renderer='templates/add_anime.pt')
def add_anime(request):
    if request.method == 'POST':
        title = request.POST['title']
        episodes = request.POST['episodes']
        genre = request.POST['genre']
        
        new_anime = Anime(title=title, episodes=episodes, genre=genre)
        DBSession = request.dbsession
        DBSession.add(new_anime)
        
        return HTTPFound(location=request.route_url('anime_list'))
    return {}

# Add similar views for edit and delete
```

## Adding Authentication

Let's secure our website with user accounts:

```bash
pip install pyramid_authsanity pyramid_redis_sessions
```

Configure authentication in `app.py`:

```python
from pyramid.authentication import AuthTktAuthenticationPolicy
from pyramid.authorization import ACLAuthorizationPolicy

def main(global_config, **settings):
    # ... existing code ...
    
    authn_policy = AuthTktAuthenticationPolicy(
        'sosecret', hashalg='sha512')
    authz_policy = ACLAuthorizationPolicy()
    
    config = Configurator(
        settings=settings,
        authentication_policy=authn_policy,
        authorization_policy=authz_policy
    )
    
    # ... rest of configuration ...
```

## Deployment

To deploy your Pyramid anime website, you can use:

1. **Development**: Continue using the built-in server for testing
2. **Production**: Use a WSGI server like Gunicorn or uWSGI with Nginx

Example with Gunicorn:
```bash
pip install gunicorn
gunicorn --paste development.ini
```

## Next Steps

To enhance your anime website, consider adding:
- User ratings and reviews
- Anime search functionality
- API endpoints for mobile apps
- Recommendation engine
- Scheduled tasks for updating anime information

This guide covers the core concepts of Pyramid while building a functional anime website. Pyramid's flexibility allows you to start small and scale up as your website grows in complexity.
