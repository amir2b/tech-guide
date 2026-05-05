#### List All Indices

```json
GET _cat/indices
```

#### Cluster status (green, yellow, red), node counts, shard stats, and task queue info

```json
GET /_cluster/health

GET /_cluster/allocation/explain

GET /_cat/shards?v&h=index,shard,prirep,state,unassigned.reason&s=state

POST /_cluster/reroute?retry_failed=true
```
