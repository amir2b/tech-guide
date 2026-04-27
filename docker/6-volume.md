# Docker Volume

Volumes are persistent data stores for containers, created and managed by Docker.

- Volume mounts
- Bind mounts
- tmpfs mounts

## Volume mounts

<https://docs.docker.com/engine/storage/volumes/>

### List volumes

```shell
docker volume ls
docker volume list
```

### Create a volume

```shell
docker volume create nginx-data

docker run -d -v nginx-data:/usr/share/nginx/html -p 8080:80 --name nginx1 nginx
docker run -d --mount source=nginx-data,target=/usr/share/nginx/html -p 8080:80 --name nginx1 nginx
docker container inspect nginx1
```

### Display detailed information on one or more volumes

```sehll
docker volume inspect nginx-data
```

### Remove one or more volumes

```shell
docker volume rm nginx-data
docker volume remove nginx-data
```

### Remove unused local volumes

```shell
docker volume prune
docker volume prune -a
```

## Bind mounts

<https://docs.docker.com/engine/storage/bind-mounts/>

```shell
docker run -d -v ./nginx-data:/usr/share/nginx/html -p 8080:80 --name nginx1 nginx
docker run -d -v "$(pwd)"/nginx-data:/usr/share/nginx/html:ro -p 8080:80 --name nginx1 nginx
```

## tmpfs mounts

<https://docs.docker.com/engine/storage/tmpfs/>

```shell
docker run -d --tmpfs /app -p 8080:80 --name nginx1 nginx
docker run -d --mount type=tmpfs,dst=/app -p 8080:80 --name nginx1 nginx
```
