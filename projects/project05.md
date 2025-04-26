# Building a CRUD Application with Django

This guide will walk you through creating a basic CRUD (Create, Read, Update, Delete) application using Django. We'll build a simple task manager where users can create, view, edit, and delete tasks.

## Prerequisites
- Python installed (3.6+ recommended)
- Basic Python knowledge
- Understanding of MVC (Model-View-Controller) concepts

## Step 1: Set Up Your Django Project

1. Create a virtual environment (recommended):
   ```bash
   python -m venv myenv
   source myenv/bin/activate  # On Windows use `myenv\Scripts\activate`
   ```

2. Install Django:
   ```bash
   pip install django
   ```

3. Create a new Django project:
   ```bash
   django-admin startproject taskmanager
   cd taskmanager
   ```

4. Create a new app (we'll call it "tasks"):
   ```bash
   python manage.py startapp tasks
   ```

5. Add the new app to `INSTALLED_APPS` in `taskmanager/settings.py`:
   ```python
   INSTALLED_APPS = [
       ...
       'tasks.apps.TasksConfig',
   ]
   ```

## Step 2: Create the Task Model

In `tasks/models.py`:

```python
from django.db import models

class Task(models.Model):
    title = models.CharField(max_length=200)
    description = models.TextField(blank=True)
    completed = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.title
```

After creating the model, run these commands to create and apply migrations:

```bash
python manage.py makemigrations
python manage.py migrate
```

## Step 3: Create the Admin Interface

1. First, create a superuser:
   ```bash
   python manage.py createsuperuser
   ```

2. Register the Task model in `tasks/admin.py`:
   ```python
   from django.contrib import admin
   from .models import Task

   admin.site.register(Task)
   ```

3. Run the development server and check the admin interface:
   ```bash
   python manage.py runserver
   ```
   Visit `http://127.0.0.1:8000/admin` and log in with your superuser credentials.

## Step 4: Create Views for CRUD Operations

In `tasks/views.py`:

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Task
from .forms import TaskForm

def task_list(request):
    tasks = Task.objects.all().order_by('-created_at')
    return render(request, 'tasks/task_list.html', {'tasks': tasks})

def task_detail(request, pk):
    task = get_object_or_404(Task, pk=pk)
    return render(request, 'tasks/task_detail.html', {'task': task})

def task_create(request):
    if request.method == 'POST':
        form = TaskForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('task_list')
    else:
        form = TaskForm()
    return render(request, 'tasks/task_form.html', {'form': form})

def task_update(request, pk):
    task = get_object_or_404(Task, pk=pk)
    if request.method == 'POST':
        form = TaskForm(request.POST, instance=task)
        if form.is_valid():
            form.save()
            return redirect('task_list')
    else:
        form = TaskForm(instance=task)
    return render(request, 'tasks/task_form.html', {'form': form})

def task_delete(request, pk):
    task = get_object_or_404(Task, pk=pk)
    if request.method == 'POST':
        task.delete()
        return redirect('task_list')
    return render(request, 'tasks/task_confirm_delete.html', {'task': task})
```

## Step 5: Create Forms

Create `tasks/forms.py`:

```python
from django import forms
from .models import Task

class TaskForm(forms.ModelForm):
    class Meta:
        model = Task
        fields = ['title', 'description', 'completed']
```

## Step 6: Create Templates

1. First, create these directories:
   ```bash
   mkdir -p tasks/templates/tasks
   ```

2. Create a base template at `tasks/templates/base.html`:
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Task Manager</title>
       <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
   </head>
   <body>
       <div class="container mt-4">
           {% block content %}
           {% endblock %}
       </div>
   </body>
   </html>
   ```

3. Create `tasks/templates/tasks/task_list.html`:
   ```html
   {% extends 'base.html' %}

   {% block content %}
   <h1>Task List</h1>
   <a href="{% url 'task_create' %}" class="btn btn-primary mb-3">Add New Task</a>
   <ul class="list-group">
       {% for task in tasks %}
       <li class="list-group-item d-flex justify-content-between align-items-center">
           <div>
               <a href="{% url 'task_detail' task.pk %}">{{ task.title }}</a>
               {% if task.completed %}
               <span class="badge bg-success ms-2">Completed</span>
               {% else %}
               <span class="badge bg-warning ms-2">Pending</span>
               {% endif %}
           </div>
           <div>
               <a href="{% url 'task_update' task.pk %}" class="btn btn-sm btn-outline-secondary">Edit</a>
               <a href="{% url 'task_delete' task.pk %}" class="btn btn-sm btn-outline-danger">Delete</a>
           </div>
       </li>
       {% endfor %}
   </ul>
   {% endblock %}
   ```

4. Create `tasks/templates/tasks/task_detail.html`:
   ```html
   {% extends 'base.html' %}

   {% block content %}
   <h1>{{ task.title }}</h1>
   <p>{{ task.description }}</p>
   <p>Status: {% if task.completed %}Completed{% else %}Pending{% endif %}</p>
   <p>Created: {{ task.created_at }}</p>
   <p>Last updated: {{ task.updated_at }}</p>
   <a href="{% url 'task_update' task.pk %}" class="btn btn-secondary">Edit</a>
   <a href="{% url 'task_list' %}" class="btn btn-primary">Back to list</a>
   {% endblock %}
   ```

5. Create `tasks/templates/tasks/task_form.html`:
   ```html
   {% extends 'base.html' %}

   {% block content %}
   <h1>{% if form.instance.pk %}Edit{% else %}Create{% endif %} Task</h1>
   <form method="post">
       {% csrf_token %}
       {{ form.as_p }}
       <button type="submit" class="btn btn-primary">Save</button>
       <a href="{% url 'task_list' %}" class="btn btn-secondary">Cancel</a>
   </form>
   {% endblock %}
   ```

6. Create `tasks/templates/tasks/task_confirm_delete.html`:
   ```html
   {% extends 'base.html' %}

   {% block content %}
   <h1>Delete Task</h1>
   <p>Are you sure you want to delete "{{ task.title }}"?</p>
   <form method="post">
       {% csrf_token %}
       <button type="submit" class="btn btn-danger">Delete</button>
       <a href="{% url 'task_list' %}" class="btn btn-secondary">Cancel</a>
   </form>
   {% endblock %}
   ```

## Step 7: Configure URLs

1. In `taskmanager/urls.py`:
   ```python
   from django.contrib import admin
   from django.urls import path, include

   urlpatterns = [
       path('admin/', admin.site.urls),
       path('', include('tasks.urls')),
   ]
   ```

2. Create `tasks/urls.py`:
   ```python
   from django.urls import path
   from . import views

   app_name = 'tasks'

   urlpatterns = [
       path('', views.task_list, name='task_list'),
       path('create/', views.task_create, name='task_create'),
       path('<int:pk>/', views.task_detail, name='task_detail'),
       path('<int:pk>/update/', views.task_update, name='task_update'),
       path('<int:pk>/delete/', views.task_delete, name='task_delete'),
   ]
   ```

## Step 8: Run and Test Your Application

1. Start the development server:
   ```bash
   python manage.py runserver
   ```

2. Visit `http://127.0.0.1:8000/` in your browser.

3. Test all CRUD operations:
   - Create new tasks
   - View task details
   - Edit existing tasks
   - Delete tasks
