
# Django Web App Authentication with Google OAuth (Gmail)

This guide explains how to set up authentication in a Django web app using Google (Gmail) OAuth alongside the traditional username and password authentication.

## Step 1: Install Dependencies

First, you need to install `django-allauth` and its dependencies.

```bash
pip install django-allauth
```

## Step 2: Add `allauth` to Installed Apps

In your `settings.py`, add the following apps:

```python
INSTALLED_APPS = [
    # Your usual apps...
    'django.contrib.sites',
    'allauth',
    'allauth.account',
    'allauth.socialaccount',
    'allauth.socialaccount.providers.google',
]
```

Also, include the following settings:

```python
SITE_ID = 1

AUTHENTICATION_BACKENDS = (
    'django.contrib.auth.backends.ModelBackend',  # Default backend for login with username and password
    'allauth.account.auth_backends.AuthenticationBackend',  # Backend for allauth
)

# Add allauth settings
LOGIN_REDIRECT_URL = '/'  # URL to redirect to after login
ACCOUNT_EMAIL_VERIFICATION = "none"
```

## Step 3: Set Up Google OAuth Credentials

To set up Google OAuth, go to the [Google Developers Console](https://console.developers.google.com/) and follow these steps:

1. **Create a new project**: Enable APIs & services for the project.
2. **Create OAuth 2.0 credentials**: Navigate to "Credentials" -> "Create credentials" -> "OAuth 2.0 Client IDs". Choose "Web application" as the application type.
3. **Authorized redirect URIs**: Add this URI for the local development environment:
   ```
   http://localhost:8000/accounts/google/login/callback/
   ```
4. **Save the client ID and client secret**: You'll need these to configure OAuth in Django.

## Step 4: Configure the Social Account Provider

In `settings.py`, configure your Google OAuth credentials:

```python
SOCIALACCOUNT_PROVIDERS = {
    'google': {
        'SCOPE': ['profile', 'email'],
        'AUTH_PARAMS': {'access_type': 'online'},
        'APP': {
            'client_id': 'YOUR_GOOGLE_CLIENT_ID',
            'secret': 'YOUR_GOOGLE_CLIENT_SECRET',
            'key': ''
        }
    }
}
```

Replace `YOUR_GOOGLE_CLIENT_ID` and `YOUR_GOOGLE_CLIENT_SECRET` with the credentials obtained from the Google Developers Console.

## Step 5: Add URLs

In your `urls.py`, include the allauth URLs:

```python
from django.urls import path, include

urlpatterns = [
    # Your other URLs
    path('accounts/', include('allauth.urls')),  # Include allauth URLs for social authentication
]
```

## Step 6: Add Google Login Button

To add a "Login with Google" button on your login page, use the following HTML:

```html
<a href="{% url 'socialaccount_login' 'google' %}">Login with Google</a>
```

This will redirect users to Google's login page for authentication.

## Step 7: Migrate and Test

Run the migrations to set up the necessary database tables:

```bash
python manage.py migrate
```

Now, test your authentication flow. You should see both the standard login form and the "Login with Google" button.

## Step 8: Optional Customization - Register with Google

You can add a "Register with Google" button to your registration page as well:

```html
<a href="{% url 'socialaccount_signup' %}?next=/">Register with Google</a>
```

Now, your Django web app supports both traditional authentication and Google OAuth (Gmail) login/registration!

---

## Notes

- Ensure you have configured your `ALLOWED_HOSTS` and `CSRF_TRUSTED_ORIGINS` settings appropriately if deploying to production.
- Don't forget to update the authorized redirect URIs in the Google Developer Console if deploying to a live server.
