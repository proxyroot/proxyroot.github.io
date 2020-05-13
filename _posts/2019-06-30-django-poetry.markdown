---
layout: post
title:  "Setup Django with poetry"
description: An introduction to setting up a django project properly with poetry.
date:   2019-05-23 21:03:36 +0530
categories: Python Django poetry
---

### Prerequisites:

- [pip](https://pip.pypa.io/en/stable/installing/)
- [python](https://www.python.org/downloads/)

### Initial setup

Lets call it arkangle]! Why not!

```bash
mkdir arkangle
cd arkangle
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

$
```
