---
layout: post
title:  "Setup Django with poetry"
description: An introduction to setting up a django project with poetry properly.
date:   2020-05-13 15:58:33 +0500
categories: Python Django poetry
---

An introduction to setting up a Django project with poetry properly.

### Prerequisites:

- [pip](https://pip.pypa.io/en/stable/installing/)
- [python](https://www.python.org/downloads/)

### Initial setup

Lets call it [arkangle](https://github.com/proxyroot/arkangle)! Cuz why not!

```bash
$ mkdir arkangle
$ cd arkangle
```

### Setup Poetry

```bash
$ poetry init

This command will guide you through creating your pyproject.toml config.

Package name [arkangle]:  
Version [0.1.0]:  
Description []:  A sample django project using poetry
Author [Afroze Khan <afroze@proxyroot.com>, n to skip]:  
License []:  
Compatible Python versions [^3.7]:  

Would you like to define your main dependencies interactively? (yes/no) [yes] no
Would you like to define your dev dependencies (require-dev) interactively (yes/no) [yes] no
Generated file

[tool.poetry]
name = "arkangle"
version = "0.1.0"
description = "A sample django project using poetry"
authors = ["Afroze Khan <afroze@proxyroot.com>"]

[tool.poetry.dependencies]
python = "^3.7"

[tool.poetry.dev-dependencies]

[build-system]
requires = ["poetry>=0.12"]
build-backend = "poetry.masonry.api"


Do you confirm generation? (yes/no) [yes] yes

$ ls
pyproject.toml

$
```

### Add dependencies

```bash
$ poetry add Django
Creating virtualenv arkangle-qZwow4NV-py3.7 in /Users/afroze.mohamed/Library/Caches/pypoetry/virtualenvs
Using version ^3.0.6 for Django

Updating dependencies
Resolving dependencies... (0.3s)

Writing lock file


Package operations: 4 installs, 0 updates, 0 removals

  - Installing asgiref (3.2.7)
  - Installing pytz (2020.1)
  - Installing sqlparse (0.3.1)
  - Installing django (3.0.6)


$ ls
poetry.lock    pyproject.toml

$
```


### Create Django project

```bash
$ poetry run django-admin startproject project

$ ls
poetry.lock    project        pyproject.toml

$
```

### Let's clean up the mess

Here is how the project structure looks like after we started a project using django-admin

```bash
arkangle/
  L poetry.lock
  L pyproject.toml
  L project
    L manage.py
    L project
      L __init__.py
      L asgi.py
      L settings.py
      L urls.py
      L wsgi.py
```

:eyes:'s Very unpleasant. Here is what i did.

```bash
$ mv project/manage.py .
$ mv project/project/* project/
$ rm -rf project/project/
```

Here is how the project structure looks like now

```bash
arkangle/
  L poetry.lock
  L pyproject.toml
  L manage.py
  L project
    L __init__.py
    L asgi.py
    L settings.py
    L urls.py
    L wsgi.py
```

### Migrations

Since we haven't created models for the admin yet, lets go ahead and migrate.

```bash
$ poetry run python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying sessions.0001_initial... OK
```

### Run Server
```bash
$ poetry run python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
May 13, 2020 - 19:49:23
Django version 3.0.6, using settings 'project.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.

```

Now you should be able to access the admin at http://127.0.0.1:8000/admin/

### Create a super user

```bash
$ poetry run python manage.py createsuperuser
Username (leave blank to use 'proxyroot'): admin
Email address: admin@proxyroot.com
Password:
Password (again):
The password is too similar to the username.
This password is too short. It must contain at least 8 characters.
This password is too common.
Bypass password validation and create user anyway? [y/N]: y
Superuser created successfully.
```

Now you should be able to login using the user you just created above
