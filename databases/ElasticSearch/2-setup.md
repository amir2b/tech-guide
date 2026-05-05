# Install Elasticsearch

## Docker Compose

<https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-docker-basic>

```yaml
services:
  elasticsearch:
    image: elasticsearch:9.3.0
    environment:
      discovery.type: single-node
      xpack.security.http.ssl.enabled: false
      ELASTIC_PASSWORD: ${ELASTIC_PASSWORD:-password}
    ports:
      - 9200:9200
    volumes:
      - elastic-data:/usr/share/elasticsearch/data

  kibana:
    image: kibana:9.3.0
    environment:
      ELASTICSEARCH_HOSTS: http://elasticsearch:9200
      ELASTICSEARCH_USERNAME: kibana_system
      ELASTICSEARCH_PASSWORD: ${KIBANA_PASSWORD:-password}
    ports:
      - 5601:5601
    depends_on:
      - elasticsearch

volumes:
  elastic-data:
```

### Reset kibana_system password

```shell
curl -X POST -u "elastic:${ELASTIC_PASSWORD:-password}" -H "Content-Type: application/json" http://127.0.0.1:9200/_security/user/kibana_system/_password -d "{\"password\":\"${KIBANA_PASSWORD:-password}\"}"
```

## Debian package

<https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-with-debian-package>

## References

- <https://hub.docker.com/_/elasticsearch>
- <https://hub.docker.com/_/kibana>
