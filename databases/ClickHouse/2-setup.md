# ClickHouse - Setup

## Docker Compose

### compose.yaml

```yaml
services:
  clickhouse:
    image: clickhouse:26.3.9.8
    container_name: clickhouse
    hostname: clickhouse
    environment:
      CLICKHOUSE_USER: user
      CLICKHOUSE_PASSWORD: password
    ports:
      - 8123:8123
      - 9000:9000
      - 9009:9009
    volumes:
      - clickhouse_data:/var/lib/clickhouse

volumes:
  clickhouse_data:
```

### Run

```shell
docker compose up -d
docker compose logs -f

## The "Delay accounting is not enabled" warning:
## ClickHouse uses this to measure OS I/O wait times more accurately for internal metrics.
sudo sh -c 'echo 1 > /proc/sys/kernel/task_delayacct'
## Make it permanent
echo 'kernel.task_delayacct=1' | sudo tee /etc/sysctl.d/90-clickhouse.conf
sudo sysctl --system
```

### Test

```shell
## Health checks
curl localhost:8123/ping

## More sophisticated health checks
curl localhost:8123/replicas_status

## Send queries from your program with POST method or GET
curl -u user:password "localhost:8123/?query=SELECT%20version()"
echo 'SELECT version()' | curl -u user:password 'http://localhost:8123' --data-binary @-

## Interactive data analysis
docker compose exec clickhouse clickhouse-client --query='SELECT version()'
```

## Ports

- **8123** - HTTP default port
- **9000** - Native Protocol port. Used by ClickHouse applications and processes like clickhouse-server, clickhouse-client, and native ClickHouse tools.
- **9009** - Inter-server communication port for low-level data access.

## Client CLI

```shell
docker compose exec clickhouse clickhouse-client --user user --password password

docker exec -it clickhouse clickhouse-client --user user --password password
```

## References

- <https://clickhouse.com/docs/install/debian_ubuntu>
- <https://clickhouse.com/docs/install/docker>
- <https://hub.docker.com/_/clickhouse>
- <https://clickhouse.com/docs/interfaces/http>
