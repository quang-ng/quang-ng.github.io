---
layout: post
title: Introducing Flask-Migrate\: Database Migration Management in Flask
tags: [python]
---


Flask-Migrate is a powerful extension for Flask that simplifies database migration management. Built on top of Alembic, a database versioning tool for SQLAlchemy, Flask-Migrate allows you to perform operations like adding, modifying, and deleting tables in your database without directly interacting with it.

## Installing Flask-Migrate

To get started with Flask-Migrate, you need to install it alongside Flask and SQLAlchemy. You can install it using pip:

```bash
pip install Flask Flask-SQLAlchemy Flask-Migrate psycopg2
```


## Configuring Flask-Migrate

1. Create Flask app

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://username:password@localhost/dbname'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)
migrate = Migrate(app, db)

```

2. Create model `user`
```python
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
```

3. Initialize Migration

```bash
flask db init
```

Generate new migration files

```bash
flask db migrate -m "Add user model"
```
Then apply to database
```bash
flask db upgrade
```