---
layout: post
title:  Django with ElasticSearch
description: Setup up a Django app with ElasticSearch as document store.
date:   2020-05-14 15:45:00 +0500
categories: Python Django poetry Elasticsearch
---

Setup up a Django app with ElasticSearch as document store.

### Introduction:

Elasticsearch is a search engine based on the Lucene library. It provides a distributed, multitenant-capable full-text search engine with an HTTP web interface and schema-free JSON documents.

Lets create a Django app which creates documents in elasticsearch automatically when a model object is created/updated/deleted.

### Initial Setup:

- Set up a django project as suggested by my previous post ""[Setup Django with poetry](https://proxyroot.com/django-poetry/)"" if you haven't already done this.
- Run single node [elasticsearch docker contianer](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html) or install [elasticsearch server](https://www.elastic.co/downloads/elasticsearch) in your machine and run it.


### Install packages:

- [django-elasticsearch-dsl](https://github.com/mjl/django-elasticsearch-dsl)
- [django-elasticsearch-dsl-drf](https://github.com/barseghyanartur/django-elasticsearch-dsl-drf)

```bash
$ poetry add <package-name>
``````


Lets create an app called `search`

```bash
$ poetry run python manage.py startapp search
```

### create model in arkangle/search/models.py

```python
from django.db import models


class Cluster(models.Model):
    id = models.IntegerField(primary_key=True)
    name = models.CharField(max_length=1000)
    nodes = models.IntegerField()
```

### create documents in arkangle/search/documents.py

```python
from django_elasticsearch_dsl import Document
from django_elasticsearch_dsl.registries import registry

from search.models import Cluster


@registry.register_document
class ClusterDocument(Document):

    class Index:
        name = "cluster"
        settings = {"number_of_shards": 1, "number_of_replicas": 0}

    class Django:
        model = Cluster
        fields = ["id", "name", "nodes"]
```

### add project settings

```python

# Application definition

INSTALLED_APPS = [
    "search.apps.SearchConfig", # Your app that you just created
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "django_elasticsearch_dsl", # added package
    "django_elasticsearch_dsl_drf", # added package
    "django_extensions"
]

ELASTICSEARCH_DSL = {
    'default': {
        'hosts': 'localhost:9200'
    },
}
```

### create migrations

```bash
$ poetry run python manage.py makemigrations
Migrations for 'search':
  search/migrations/0001_initial.py
    - Create model Cluster

$
```

### Run Migrations

```bash
$ poetry run python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, search, sessions
Running migrations:
  Applying search.0001_initial... OK

$
```

### Create/Re-build elasticsearch indicies

```bash
$ poetry run python manage.py search_index --rebuild
Are you sure you want to delete the 'cluster' indexes? [n/Y]: Y
Deleting index 'cluster'
Creating index 'cluster'
Indexing 1 'Cluster' objects

$
```

`Note: Make sure your elasticsearch server is running`

### Lets play with some data

Get on to the django shell

```bash
$ poetry run python manage.py shell
```

Create some documents

```python
>>> from search.models import Cluster


>>> cluster = Cluster(name="Amazon", nodes=130)
>>> cluster.save()

>>> cluster = Cluster(name="Microsoft", nodes=90)
>>> cluster.save()

>>> cluster = Cluster(name="Google", nodes=10)
>>> cluster.save()

>>> cluster = Cluster(name="Digital Ocean", nodes=45)
>>> cluster.save()

>>> cluster = Cluster(name="Github", nodes=200)
>>> cluster.save()
```

Lets search

```python
>>> from search.documents import ClusterDocument

>>> hits = ClusterDocument.search().query("match", name="Amazon")
>>>
>>> for hit in hits:
        print(hit.name, ":", hit.nodes)

Amazon : 100
Amazon : 130
>>>
```

Hooray :tada: !!!

Lets update existing object

```python
>>> cluster = Cluster.objects.filter(name="Github").first()
>>> cluster.name
"Github"
>>> cluster.name = "GrubHub"
>>> cluster.save()
```

Search for the updated record

```python
>>> from search.documents import ClusterDocument

>>> hits = ClusterDocument.search().query("match", name="GrubHub")
>>>
>>> for hit in hits:
        print(hit.name, ":", hit.nodes)

GrubHub : 200
>>>
```

We finally got one of our models indexed in elasticsearch. Find the code samle for above at [TheArkangel](https://github.com/proxyroot/arkangle). For the sake of visibility, i have also created a view in the app that shows the documents.
