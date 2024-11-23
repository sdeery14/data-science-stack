# Working with Database using SQLAlchemy

## Introduction
This guide will help you set up and work with a database using SQLAlchemy in your Python projects. SQLAlchemy is a SQL toolkit and Object-Relational Mapping (ORM) library for Python, which provides a full suite of well-known enterprise-level persistence patterns.

## Prerequisites
Before you begin, ensure you have the following installed:
- [Python](https://www.python.org/downloads/) - Ensure you have Python installed on your system.
- [Poetry](https://python-poetry.org/docs/#installation) - For managing dependencies and virtual environments.
- [Docker Desktop](https://www.docker.com/products/docker-desktop) - Required to run the PostgreSQL database in a container.

## Set Up the Environment
1. **Install Poetry**:
    ```bash
    pip install poetry
    ```

2. **Create a New Poetry Project**:
    Run this command in your project's root directory.
    ```bash
    poetry new sqlalchemy_app
    cd sqlalchemy_app
    ```

    The project directory should now look like this:
    ```
    - `root/`
        - `sqlalchemy_app/`
            - `.venv/`
            - `sqlalchemy_app/`
                - `__init__.py`
            - `tests/`
                - `__init__.py`
            - `poetry.lock`
            - `pyproject.toml`
            - `README.md`
    ```

3. **Add SQLAlchemy and psycopg2 as Dependencies**:
    ```bash
    poetry add sqlalchemy psycopg2-binary
    ```

## Set Up the Database
1. **Add database details as environment variables**:
    Create a file named `.env` in the `sqlalchemy_app/` directory and add the following variables.
    ```env
    # SQLAlchemy application database configuration
    DB_USER=sqlalchemy_user
    DB_PASSWORD=sqlalchemy_password
    DB_NAME=sqlalchemy_db
    DB_HOST=db_service
    DB_PORT=5432
    DB_URI=postgresql://${DB_USER}:${DB_PASSWORD}@${DB_HOST}:${DB_PORT}/${DB_NAME}
    ```

    The project directory should now look like this:
    ```
    - `root/`
        - `sqlalchemy_app/`
            - `.venv/`
            - `sqlalchemy_app/`
                - `__init__.py`
            - `tests/`
                - `__init__.py`
            - `.env`
            - `poetry.lock`
            - `pyproject.toml`
            - `README.md`
    ```

2. **Create an instance of Postgres with Docker**:
    Create a file named `docker-compose.yaml` in the `sqlalchemy_app/` directory and add the following.
    ```yaml
    services:
        db_service:
            container_name: db_service
            image: postgres:16
            environment:
                POSTGRES_USER: ${DB_USER}
                POSTGRES_PASSWORD: ${DB_PASSWORD}
                POSTGRES_DB: ${DB_NAME}
            ports:
                - "5432:5432"
            volumes:
                - postgres_data:/var/lib/postgresql/data
    volumes:
        postgres_data:
    ```

    The project directory should now look like this:
    ```
    - `root/`
        - `sqlalchemy_app/`
            - `.venv/`
            - `sqlalchemy_app/`
                - `__init__.py`
            - `tests/`
                - `__init__.py`
            - `.env`
            - `docker-compose.yaml`
            - `poetry.lock`
            - `pyproject.toml`
            - [README.md](http://_vscodecontentref_/3)
    ```

3. **Start the Postgres container**:
    Open Docker Desktop and then run the command below from within the `sqlalchemy_app` directory to start the Postgres container.
    ```bash
    docker-compose up -d
    ```

4. **Verify the database is running**:
    To verify that the database is running and accessible, you can log into the PostgreSQL database using the `psql` command-line tool. Run the following command:
    ```bash
    docker exec -it db_service psql -U sqlalchemy_user -d sqlalchemy_db
    ```

    You should see a prompt like this:
    ```plaintext
    psql (16)
    Type "help" for help.

    sqlalchemy_db=#
    ```

    From here, you can run SQL commands to interact with your database. For example, to list all tables, you can use:
    ```sql
    \dt
    ```

    To exit the `psql` shell, type:
    ```sql
    \q
    ```

## Create the Data Models
1. **Create the Data Models**:
    Create a file named `models.py` in the `sqlalchemy_app` directory and add the following code:
    ```python
    from sqlalchemy import create_engine, Column, Integer, String, Float
    from sqlalchemy.ext.declarative import declarative_base
    from sqlalchemy.orm import sessionmaker
    import os

    DATABASE_URI = os.getenv('DB_URI')
    engine = create_engine(DATABASE_URI)
    Session = sessionmaker(bind=engine)
    Base = declarative_base()

    class Data(Base):
        __tablename__ = 'data'
        id = Column(Integer, primary_key=True)
        name = Column(String(80), unique=True, nullable=False)
        value = Column(Float, nullable=False)

    if __name__ == '__main__':
        Base.metadata.create_all(engine)
    ```

2. **Initialize the Database**:
    Run the following command to create the database schema:
    ```bash
    poetry run python sqlalchemy_app/models.py
    ```

## Verify Database Schema
To verify that the database schema was created successfully, follow these steps:

1. **Log into the PostgreSQL Docker Container**:
    Run the following command to access the PostgreSQL container:
    ```bash
    docker exec -it db_service psql -U sqlalchemy_user -d sqlalchemy_db
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

## Conclusion
This guide provides a comprehensive overview of setting up and working with a database using SQLAlchemy in your Python projects. By following these steps, you can ensure a smooth and efficient workflow for managing your database interactions.