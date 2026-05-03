# PostgreSQL - Setup

## Setup PostgreSQL with Docker

```shell
docker pull postgres:18.2-alpine
docker run --rm -d -e POSTGRES_PASSWORD=password -p 5432:5432 --name postgres1 postgres:18.2-alpine
```

### Connect to PostgreSQL

```shell
docker exec -it postgres1 psql -U postgres
```

## Setup PostgreSQL with Docker Compose

### compose.yaml

```yaml
services:
  postgres:
    image: postgres:18.2-alpine
    ports:
      - 5432:5432
    environment:
      TZ: Asia/Tehran
      POSTGRES_PASSWORD: password
    volumes:
      - postgres-data:/var/lib/postgresql
      - ./initdb.d:/docker-entrypoint-initdb.d
      - ./backup:/backup

  pgadmin:
    image: dpage/pgadmin4:9.12
    ports:
      - 8181:80
    environment:
      # Required by pgAdmin
      PGADMIN_DEFAULT_EMAIL: demo@example.com
      PGADMIN_DEFAULT_PASSWORD: secret
      # Don't require the user to login
      PGADMIN_CONFIG_SERVER_MODE: 'False'
      # Don't require a "master" password after logging in
      PGADMIN_CONFIG_MASTER_PASSWORD_REQUIRED: 'False'
    depends_on:
      - postgres

  adminer:
    image: adminer:standalone
    ports:
      - 8080:8080
    depends_on:
      - postgres

volumes:
  postgres-data:
```

### Run

```shell
docker compose up -d
docker compose logs -f
docker compose exec postgres psql -U postgres
```

### Initialization scripts

/docker-entrypoint-initdb.d/init-user-db.sh

```shell
#!/usr/bin/env bash
set -e

psql -v ON_ERROR_STOP=1 --username "$POSTGRES_USER" --dbname "$POSTGRES_DB" <<-EOSQL
  CREATE USER docker WITH PASSWORD 'password';
  CREATE DATABASE docker;
  GRANT ALL PRIVILEGES ON DATABASE docker TO docker;
EOSQL
```

## Setup PostgreSQL with APT

<https://www.postgresql.org/download/linux/ubuntu/>

```shell
sudo apt update
sudo apt install postgresql
```

## References

- <https://www.postgresql.org/docs/current/>
- <https://docs.docker.com/guides/pgadmin/>
