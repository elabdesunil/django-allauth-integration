# Django-allauth integration

Table of Contents
- [Django-allauth integration](#django-allauth-integration)
  - [Installation Guide](#installation-guide)
        - [Setup a virtual environment -](#setup-a-virtual-environment--)
      - [Install the requirements](#install-the-requirements)
      - [Migrate database](#migrate-database)
      - [Create superuser](#create-superuser)
  - [Build up from the beginning guide](#build-up-from-the-beginning-guide)
    - [Add some lines in `INSTALLED_APPS`](#add-some-lines-in-installed_apps)
    - [Add `django-allauth` package to `urls.py`](#add-django-allauth-package-to-urlspy)
    - [Migrate the changes to update the database - `python manage.py migrate`](#migrate-the-changes-to-update-the-database---python-managepy-migrate)
    - [Setup the site in Django admin dashboard](#setup-the-site-in-django-admin-dashboard)
  - [Creating Homepage](#creating-homepage)
    - [inside config create a file `views.py`](#inside-config-create-a-file-viewspy)
    - [add the following in `views.py`](#add-the-following-in-viewspy)
    - [Setup django to look into config folder.](#setup-django-to-look-into-config-folder)
    - [create `template/home.html` in config/](#create-templatehomehtml-in-config)
    - [in `urls.py` add the following lines](#in-urlspy-add-the-following-lines)
  - [Run](#run)

## Installation Guide

##### Setup a virtual environment -
for windows the commands are as follows

```
pip -m venv .env
.env\Scripts\activate
```

#### Install the requirements

```
pip install -r requirements.txt
```

#### Migrate database 
`python manage.py migrate`
#### Create superuser 
`python manage.py createsuperuser`

Follow steps in the [section](#Setup-the-site-in-Django-admin-dashboard)

`python manage.py runserver`

## Build up from the beginning guide

```
mkdir django-allauth-integration
cd django-allauth-integration

pip -m venv .env
.env\Scripts\activate

pip install django~=3.1.0
django-admin startproject config .
python manage.py migrate
python manage.py runserver

pip install django-allauth==0.43.0
```

### Add some lines in `INSTALLED_APPS`

```python
# new additions for django-allauth
    'django.contrib.sites',
    'allauth',
    'allauth.account',
    'allauth.socialaccount',
    'allauth.socialaccount.providers.github',
```

At the bottom of the `settings.py`, add the following codes

```python
# Specify that we are using allauth backend

AUTHENTICATION_BACKENDS = (
    "django.contrib.auth.backends.ModelBackend",
    "allauth.account.auth_backends.AuthenticationBackend",
)

SITE_ID = 1

ACCOUNT_EMAIL_VERIFICATION = 'none'

LOGIN_REDIRECT_URL = 'home'
```

### Add `django-allauth` package to `urls.py`

```python
    from django.urls import include
    path('accounts/', include('allauth.urls')),
```

### Migrate the changes to update the database - `python manage.py migrate`

Set up superuser
`python manage.py createsuperuser`

### Setup the site in Django admin dashboard

1. Sites >> Set Domain Name to 127.0.0.1 or localhost
2. Social Applications >> Add >> Select Github as Provider and add Client ID and Secret ID. Finally select localhost or 127.0.0.1 for chosen sites
   Note: Double click or click '+' sign to add the site.

## Creating Homepage

### inside config create a file `views.py`

```
touch views.py
```

### add the following in `views.py`

```python
from django.views.generic import TemplateView


class Home(TemplateView):
    template_name = 'home.html'
```

### Setup django to look into config folder. 
This is because by default Django will look within an app for the templates folder.
in `settings.py`

```python
TEMPLATES = [
    ...
    'DIRS': [os.path.join(BASE_DIR, 'config/templates')],
    ...
]
```

don't forget to import os at the top of `settings.py`

### create `template/home.html` in config/
in `home.html`

```python
{% load socialaccount %}

<h1>Django Allauth Tutorial</h1>
{% if user.is_authenticated %}
<p>Welcome {{ user.username }} !!!</p>
{% else %}
<a href="{% provider_login_url 'github' %}">Sign Up</a>
{% endif %}
```

### in `urls.py` add the following lines

```python
from .views import Home

urlpatters = [
    # ..

    path('', Home.as_view(), name='home'),
]
```

## Run

`python manage.py runserver`
