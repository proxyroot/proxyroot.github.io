---
layout: post
title:  "Setup Django with poetry"
description: An introduction to setting up a django project properly with poetry.
date:   2019-05-23 21:03:36 +0530
categories: Python Django poetry
---

An introduction to setting up a Django project properly with poetry.

### Prerequisites:

- [pip](https://pip.pypa.io/en/stable/installing/)
- [python](https://www.python.org/downloads/)

### Initial setup

Lets call it [arkangle](#)! Why not!

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

### Lets clean what just happened
Here is how the project structure looks like after the above command
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

### A few things to change further
Since we have moved things around we need to change a few things as below
