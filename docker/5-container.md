# Docker Container

<https://docs.docker.com/reference/cli/docker/container/>

## Commands

### Create and run a new container from an image

<https://docs.docker.com/reference/cli/docker/container/run/>

```shell
docker container run -p 8080:80 nginx
docker run -d -p 8080:80 nginx
docker run -d -p 8080:80 --name nginx1 nginx
docker run -d --rm -p 8080:80 --name nginx1 nginx
```

### List containers

```shell
docker container ls
docker ps
docker ps -a
```

### Display a live stream of container(s) resource usage statistics

```shell
docker container stats
docker container stats nginx1
```

### Display the running processes of a container

```shell
docker container top nginx1
```

### Pause all processes within one or more containers

```shell
docker container pause nginx1
docker pause nginx1
```

### Unpause all processes within one or more containers

```shell
docker container unpause nginx1
docker unpause nginx1
```

### Stop one or more running containers

```shell
docker container stop nginx1
docker stop nginx1
```

### Kill one or more running containers

```shell
docker container kill nginx1
docker kill nginx1
```

### Restart one or more containers

```shell
docker container restart nginx1
docker restart nginx1
```

### Remove one or more containers

```shell
docker container rm nginx1
docker rm nginx1
```

### Attach local standard input, output, and error streams to a running container

```shell
docker container attach nginx1
docker container attach --sig-proxy=false nginx1
```

### Fetch the logs of a container

```shell
docker container logs nginx1
docker logs -f nginx1
```

### Execute a command in a running container

```shell
docker container exec -it nginx1 bash
docker exec -it nginx1 sh -c "echo a && echo b"
```

### Copy files/folders between a container and the local filesystem

```shell
echo "<html><head><title>Hello</title></head><body><h1>Hi</h1></body></html>" > index.html
docker container cp index.html nginx1:/usr/share/nginx/html/

docker cp nginx1:/etc/nginx/conf.d/default.conf /tmp/
```

### Inspect changes to files or directories on a container's filesystem

```shell
docker container diff nginx1
```

### Display detailed information on one or more containers

```shell
docker container inspect nginx1
```

### Remove all stopped containers

```shell
docker container prune
```

## Sample: PostgreSQL

```shell
docker pull postgres:18.2-alpine
docker run -d -e POSTGRES_PASSWORD=password -p 5432:5432 --name postgres1 postgres:18.2-alpine
```
