# Elasticsearch

Elasticsearch is a distributed, RESTful search and analytics engine capable of solving a growing number of use cases.

<img src="./files/elasticsearch-logo.png" width=200 alt="Elasticsearch Logo" />

## Core Concepts and Architecture

- Documents
- Indices
- Shards
- Replicas
- Nodes
- Clusters

```text
┌──────────────────────────────────────────┐
│            Elasticsearch Cluster         │
├──────────────────────────────────────────┤
│   Node 1         Node 2         Node 3   │
│ ┌────────┐     ┌────────┐     ┌────────┐ │
│ │ Shard 1│     │ Shard 2│     │ Shard 3│ │
│ │(Primary│     │(Primary│     │(Primary│ │
│ │  for   │     │  for   │     │  for   │ │
│ │Index A)│     │Index A)│     │Index A)│ │
│ └────────┘     └────────┘     └────────┘ │
│ ┌────────┐     ┌────────┐     ┌────────┐ │
│ │Shard 2 │     │Shard 3 │     │Shard 1 │ │
│ │(Replica│     │(Replica│     │(Replica│ │
│ │ of B)  │     │ of B)  │     │ of B)  │ │
│ └────────┘     └────────┘     └────────┘ │
└──────────────────────────────────────────┘
```

### Sharding

- Primary Shards: Main data partitions (set at index creation).
- Each shard is an independent Lucene index.
- Sharding benefits:
  - Horizontal scaling
  - Parallel processing
  - Distribution across nodes

### Replication

- Replica Shards: Copies of primary shards
- Replication benefits:
  - High availability (failover)
  - Increased search throughput
  - Data redundancy

#### Configuration Example

```json
{
  "settings": {
    "index": {
      "number_of_shards": 3,      // Fixed at creation
      "number_of_replicas": 2      // Can be changed
    }
  }
}
```

## Node Role (Key Configuration `node.roles`)

<https://www.elastic.co/docs/deploy-manage/distributed-architecture/clusters-nodes-shards/node-roles>

- Master-Eligible Node: "master", "voting_only"
- Data Node: "data", "data_content", "data_hot", "data_warm", "data_cold", "data_frozen"
- Ingest Node: "ingest"
- Remote-eligible node: "remote_cluster_client"
- Machine Learning (ML) Node: "ml"
- Transform Node: "transform"
- Coordinating Only Node: "" (an empty)

### Write Operation Flow

1. Request goes to coordinating node
2. Coordinating node routes to primary shard
3. Primary writes and forwards to replicas
4. Success when all (or configured) replicas acknowledge

## The Elastic Stack (ELK)

- Elasticsearch
- Logstash
- Kibana
- Beats <https://www.elastic.co/beats>

## References

- <https://www.elastic.co/docs/reference/elasticsearch/rest-apis>
