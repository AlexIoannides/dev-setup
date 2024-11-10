# Docker Compose Configs

We may need ancillary containerised services up-and-running for development (e.g., databases), or it might be easier to configure a single container (e.g., with volume mounts, environment variables, etc.), in a single file. We can use [Docker Compose](https://docs.docker.com/compose/) for this task - a tool for defining and running multi-container applications.

## Docker Compose Basics

Basic commands for use with any system configuration.

### Starting

To run all containers as a detached background processes that a tool like VS Code can connect to remotely:

```text
docker compose -f dev.yaml up --detach
```

### Connecting to a Shell in a Running Container

```text
docker exec -it NAME_OF_CONTAINER zsh
```

### Stopping

This will preserve the state of all containers (if they have been modified it in any way).

```text
docker compose -f dev.yaml stop
```

### Deleting

To delete all stopped containers in the system,

```text
docker compose -f dev.yaml down -v
```

Where the `-v` flag will delete any volumes created.

## Available Configs

A brief summary of the system configs defined in this directory (in YAML files).

### Dev-Workstation

`dev.yaml` ➜ Lightweight Ubuntu developer container with `.ssh` and `workspace` mounted volumes and a `dev` user with super-user privileges.

### Dev-Workstation + Postgres

`dev-postgres.yaml` ➜ Developer container networked to a Postgres instance backed by Docker volume storage.

#### Postgres 101

Login to the database on the Postgres container:

```text
psql -U admin -d postgres
```

Creating a table in the `postgres` DB:

```sql
CREATE TABLE person (
    first_name text,
    second_name text,
    age_years int
);
```

Populating the table with values:

```sql
INSERT INTO person VALUES ('Alex', 'Ioannides', 44);
INSERT INTO person VALUES ('Bianca', 'Ioannides', 46);
INSERT INTO person VALUES ('Hector', 'Ioannides', 12);
```

List all tables in schema:

```text
\dt
```

#### Postgres & Python

Connecting to Postgres using [psycopg2](https://www.psycopg.org/docs/index.html) - make sure the `psycopg2-binary` package is installed, then:

```python
import os

import psycopg2

# Connect to the PostgreSQL database
connection = psycopg2.connect(
    dbname=os.getenv("DB_NAME"),
    user=os.getenv("DB_USER"),
    password=os.getenv("DB_PASSWORD"),
    host=os.getenv("DB_HOST"),
    port=5432
)

# Create a cursor object
cursor = connection.cursor()

# Example 1: Execute a SELECT query
cursor.execute("SELECT * FROM person;")
rows = cursor.fetchall()
for row in rows:
    print(row)

# Example 2: Execute an INSERT query
cursor.execute(
    "INSERT INTO person (first_name, second_name, age_years) VALUES (%s, %s, %s);",
    ("Marios", "Ioannides", 40)
)
connection.commit()
```

Connecting to Postgres using [SQLAlchemy](https://www.sqlalchemy.org) - make sure the `sqlalchemy` package is installed, then:

```python
import os

from sqlalchemy import create_engine, text
from sqlalchemy.orm import Session

# Define the database URL
DATABASE_URL = (
    f"postgresql://{os.getenv('DB_USER')}:{os.getenv('DB_PASSWORD')}@"
    f"{os.getenv('DB_HOST')}:{os.getenv('DB_PORT')}/{os.getenv('DB_NAME')}"
)

# Create an engine
engine = create_engine(DATABASE_URL)

# Example 1: Executing a SELECT query
with Session(engine) as session:
    result = session.execute(text("SELECT * FROM person"))
    rows = result.fetchall()
    for row in rows:
        print(row)

# Example 2: Executing an INSERT query
with Session(engine) as session:
    session.execute(
        text(
            "INSERT INTO person (first_name, second_name, age_years) "
            "VALUES (:v1, :v2, :v3)"
        ),
        {"v1": "Yvonne", "v2": "Levy", "v3": "83"}
    )
    session.commit()
```
