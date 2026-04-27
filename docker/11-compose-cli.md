# Docker Compose

## Commands

### Pull service images

```shell
docker compose pull
```

### Create and start containers

```shell
docker compose up
docker compose up -d
```

### Stop and remove containers, networks

```shell
docker compose down
docker compose down -v
```

### Run a one-off command on a service

```shell
docker compose run [OPTIONS] SERVICE [COMMAND] [ARGS...]
docker compose run postgres bash
```

### Execute a command in a running container

```shell
docker compose exec [OPTIONS] SERVICE COMMAND [ARGS...]
docker compose exec
docker compose exec postgres bash
```

### Restart service containers

```shell
docker compose restart
```

### View output from containers

```shell
docker compose logs [OPTIONS] [SERVICE...]
docker compose logs
docker compose logs -f
docker compose logs -f postgres
```

### List containers

```shell
docker compose ps
docker compose ps -a
```

### Display a live stream of container(s) resource usage statistics

```shell
docker compose stats
```

### Build or rebuild services

```shell
docker compose build
```
