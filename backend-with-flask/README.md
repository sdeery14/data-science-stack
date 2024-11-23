# Flask for Data Science Projects

This guide will help you set up a Flask application for data science projects, including setting up the database schema, creating a static site, and integrating with React.

## Table of Contents
1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Set Up the Environment](#set-up-the-environment)
4. [Set Up the Database](#set-up-the-database)

## Introduction
Flask is a lightweight WSGI web application framework in Python. It is designed with simplicity and flexibility in mind, making it a great choice for data science projects.

## Prerequisites

Before you begin, ensure you have the following installed:

- [Docker Desktop](https://www.docker.com/products/docker-desktop) - Required to run the PostgreSQL database in a container.
- [Poetry](https://python-poetry.org/docs/#installation) - For managing dependencies and virtual environments.
- [Python](https://www.python.org/downloads/) - Ensure you have Python installed on your system.

## Set Up the Environment
1. **Install Poetry**:

```bash
pip install poetry
```

2. **Create a New Poetry Project**:

Run this command in your project's root directory.

```bash
poetry new flask_app
cd flask_app
```

The project directory should now look like this:

    - `root/`
        - `flask_app/`
            - `.venv/`
            - `app/`
                - `__init__.py`
            - `tests/`
                - `__init__.py`
            - `poetry.lock`
            - `pyproject.toml`
            - `README.md`

3. **Add Flask as a Dependency**:
```bash
poetry add flask
```

4. **Create a Flask App**:

Add the following to the `__init__.py` file within the `app` directory.
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, Flask!"

if __name__ == '__main__':
    app.run(debug=True)
```

5. **Run the App**:

Run the app from within the `flask_app/ `directory.
```bash
poetry run python app.py
```
The app should now be accessible at http://127.0.0.1:5000/ and it should output "Hello, Flask!" in the browser.

## Set Up the Database
1. **Add database details as environemnt variables**

Create a file named `.env` in the `flask_app/` directory and add the following variables.
```env
# Flask application database configuration
FLASK_DB_USER=flask_user
FLASK_DB_PASSWORD=flask_password
FLASK_DB_NAME=flask_db
FLASK_DB_HOST=flask_db_service
FLASK_DB_PORT=5432
FLASK_DB_URI=postgresql://${FLASK_DB_USER}:${FLASK_DB_PASSWORD}@${FLASK_DB_HOST}:${FLASK_DB_PORT}/${FLASK_DB_NAME}
```

The project directory should now look like this:

    - `root/`
        - `flask_app/`
            - `.venv/`
            - `app/`
                - `__init__.py`
            - `tests/`
                - `__init__.py`
            - `.env`
            - `poetry.lock`
            - `pyproject.toml`
            - `README.md`

2. **Create an instance of Postgres with Docker**

Create a file named `docker-compose.yaml` in the `flask_app/` directory and add the following.
```yaml
services:
    # PostgreSQL database service for Flask
    flask_db_service:
        container_name: flask_db_service
        image: postgres:16
        environment:
            POSTGRES_USER: ${FLASK_DB_USER}
            POSTGRES_PASSWORD: ${FLASK_DB_PASSWORD}
            POSTGRES_DB: ${FLASK_DB_NAME} 
        ports:
            - "5432:5432"  # Map port 5432 on the host to port 5432 in the container
        volumes:
            - flask_postgres_data:/var/lib/postgresql/data  # Persist database data
volumes:
    postgres_data:
```
The project directory should now look like this:

    - `root/`
        - `flask_app/`
            - `.venv/`
            - `app/`
                - `__init__.py`
            - `tests/`
                - `__init__.py`
            - `.env`
            - `docker-compose.yaml`
            - `poetry.lock`
            - `pyproject.toml`
            - `README.md`

Open Docker Desktop and then run the command below from within the `flask_app` directory to start the Postgres container.

```bash
docker-compose up -d
```

To verify that the database is running and accessible, you can log into the PostgreSQL database using the `psql` command-line tool. Run the following command:

```bash
docker exec -it flask_db_service psql -U flask_user -d flask_db
```

You should see a prompt like this:

```plaintext
psql (13.3)
Type "help" for help.

flask_db=#
```

From here, you can run SQL commands to interact with your database. For example, to list all tables, you can use:

```sql
\dt
```

To exit the `psql` shell, type:

```sql
\q
```


3. **Add SQLAlchemy and psycopg2 as Dependencies**:
```bash
poetry add flask-sqlalchemy flask-migrate psycopg2-binary python-dotenv
```

4. **Create the Data Models**:

Create a file named `models.py` in the `app` directory and add the following code:

```python
from app import db

class Data(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(80), unique=True, nullable=False)
    value = db.Column(db.Float, nullable=False)

```

Next, update the `__init__.py` file in the `app` directory to include the database initialization:

```python
from flask import Flask
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate
from dotenv import load_dotenv
import os

load_dotenv()

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = os.getenv('FLASK_DB_URI')
db = SQLAlchemy(app)
migrate = Migrate(app, db)

from app import models

@app.route('/')
def home():
    return "Hello, Flask!"

if __name__ == '__main__':
    app.run(debug=True)

```

Finally, run the following commands to migrate the database:

```bash
poetry run flask db init
poetry run flask db migrate -m "Initial migration."
poetry run flask db upgrade
```
## Verify Database Schema

To verify that the database schema was created successfully, follow these steps:

1. **Log into the PostgreSQL Docker Container**:

Run the following command to access the PostgreSQL container:

```bash
docker exec -it flask_db_service psql -U flask_user -d flask_db
```

2. **Check the Schema**:

Once you are inside the `psql` shell, list all tables to verify that the schema was created:

```sql
\dt
```

You should see a table named `data` in the list. To view the columns of the `data` table, use:

```sql
\d data
```

This will display the structure of the `data` table, including the columns `id`, `name`, and `value`.

3. **Exit the `psql` Shell**:

To exit the `psql` shell, type:

```sql
\q
```
