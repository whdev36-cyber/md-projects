# Django Authentication: A Comprehensive Guide

Django provides a robust authentication system out of the box that handles user accounts, groups, permissions, and cookie-based user sessions.

## Core Components

### 1. User Model
Django's default authentication uses the `User` model with these key fields:
- `username`
- `password`
- `email`
- `first_name`
- `last_name`
- `is_active`
- `is_staff`
- `is_superuser`
- `last_login`
- `date_joined`

You can also create a custom user model (recommended for new projects):
```python
from django.contrib.auth.models import AbstractUser

class CustomUser(AbstractUser):
    # Add additional fields here
    bio = models.TextField(max_length=500, blank=True)
    birth_date = models.DateField(null=True, blank=True)
```

## Setting Up Authentication

### 1. Configuration
In `settings.py`:
```python
INSTALLED_APPS = [
    ...
    'django.contrib.auth',  # Core authentication framework
    'django.contrib.contenttypes',  # Required by auth
    ...
]

AUTH_USER_MODEL = 'myapp.CustomUser'  # If using custom user model

# Authentication backends
AUTHENTICATION_BACKENDS = [
    'django.contrib.auth.backends.ModelBackend',
]

# Login/Logout URLs
LOGIN_URL = '/login/'
LOGIN_REDIRECT_URL = '/profile/'
LOGOUT_REDIRECT_URL = '/'
```

## User Management

### 1. Creating Users
```python
from django.contrib.auth import get_user_model

# Create regular user
user = get_user_model().objects.create_user(
    username='john',
    email='john@example.com',
    password='s3cr3t'
)

# Create superuser (from command line: python manage.py createsuperuser)
admin = get_user_model().objects.create_superuser(
    username='admin',
    email='admin@example.com',
    password='supers3cr3t'
)
```

### 2. Authentication Views
Django provides built-in views for common auth operations:
```python
from django.contrib.auth import views as auth_views

urlpatterns = [
    path('login/', auth_views.LoginView.as_view(), name='login'),
    path('logout/', auth_views.LogoutView.as_view(), name='logout'),
    path('password_change/', auth_views.PasswordChangeView.as_view(), name='password_change'),
    path('password_change/done/', auth_views.PasswordChangeDoneView.as_view(), name='password_change_done'),
    path('password_reset/', auth_views.PasswordResetView.as_view(), name='password_reset'),
    path('password_reset/done/', auth_views.PasswordResetDoneView.as_view(), name='password_reset_done'),
    path('reset/<uidb64>/<token>/', auth_views.PasswordResetConfirmView.as_view(), name='password_reset_confirm'),
    path('reset/done/', auth_views.PasswordResetCompleteView.as_view(), name='password_reset_complete'),
]
```

## Using Authentication in Views

### 1. Checking Authentication
```python
from django.contrib.auth.decorators import login_required

@login_required
def profile_view(request):
    return render(request, 'profile.html')
```

### 2. Checking Permissions
```python
from django.contrib.auth.decorators import permission_required

@permission_required('polls.can_vote')
def vote_view(request):
    # Your view logic
```

### 3. Class-Based View Example
```python
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import TemplateView

class ProfileView(LoginRequiredMixin, TemplateView):
    template_name = 'profile.html'
    login_url = '/login/'
    redirect_field_name = 'redirect_to'
```

## Forms for Authentication

### 1. User Creation Form
```python
from django.contrib.auth.forms import UserCreationForm
from .models import CustomUser

class CustomUserCreationForm(UserCreationForm):
    class Meta:
        model = CustomUser
        fields = ('username', 'email')
```

### 2. Authentication Form
```python
from django.contrib.auth.forms import AuthenticationForm

def login_view(request):
    if request.method == 'POST':
        form = AuthenticationForm(request, data=request.POST)
        if form.is_valid():
            # Log the user in
            login(request, form.get_user())
            return redirect('home')
    else:
        form = AuthenticationForm()
    return render(request, 'login.html', {'form': form})
```

## Advanced Topics

### 1. Social Authentication
Using packages like `django-allauth`:
```python
# settings.py
INSTALLED_APPS += [
    'allauth',
    'allauth.account',
    'allauth.socialaccount',
    'allauth.socialaccount.providers.google',
]

AUTHENTICATION_BACKENDS = [
    'django.contrib.auth.backends.ModelBackend',
    'allauth.account.auth_backends.AuthenticationBackend',
]

# urls.py
urlpatterns += [
    path('accounts/', include('allauth.urls')),
]
```

### 2. JWT Authentication
For API authentication with Django REST Framework:
```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}

# urls.py
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
]
```

### 3. Custom Authentication Backends
```python
class EmailAuthBackend:
    def authenticate(self, request, username=None, password=None):
        try:
            user = User.objects.get(email=username)
            if user.check_password(password):
                return user
            return None
        except User.DoesNotExist:
            return None

    def get_user(self, user_id):
        try:
            return User.objects.get(pk=user_id)
        except User.DoesNotExist:
            return None
```

## Best Practices

1. Always use `get_user_model()` instead of directly importing the User model
2. Hash passwords properly (Django does this automatically)
3. Use HTTPS for all authentication-related pages
4. Implement proper session management
5. Consider rate limiting for login attempts
6. Keep user-related views and templates secure
