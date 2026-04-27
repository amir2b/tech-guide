# Build a Docker Image

Docker builds images by reading the instructions from a Dockerfile.

- .dockerignore files

## Commands

### Build an image from a Dockerfile

```shell
docker image build -t app1 .
docker image build --build-arg <arg_name>=<value> -t app1 .

docker image run --env <env_name>=<value> app1
```

### Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

```shell
docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
docker image tag nginx nginx1
docker image tag nginx repository:8082/nginx1
```

### Upload an image to a registry

```shell
docker loign repository:8082

docker image push [OPTIONS] NAME[:TAG]
docker image push repository:8082/nginx1

docker image pull repository:8080/nginx1
```
