# Time-Series & Log Analytics Patterns

## Indexing Strategy - Time-Based Indices:

- logs-2026.01.01
- logs-2026.01.02
- logs-2026.01.03

#### Index Template for Time-Series

```json
PUT _index_template/logs-template
{
  "index_patterns": ["logs-*"],
  "template": {
    "settings": {
      "number_of_shards": 2,
      "number_of_replicas": 1,
      "index.lifecycle.name": "logs_policy"
    },
    "mappings": {
      "properties": {
        "@timestamp": { "type": "date" },
        "message": { "type": "text" },
        "level": { "type": "keyword" },
        "host": { 
          "type": "object",
          "properties": {
            "name": { "type": "keyword" },
            "ip": { "type": "ip" }
          }
        },
        "metrics": {
          "type": "object",
          "dynamic": true
        }
      }
    }
  }
}
```

## Index Lifecycle Management (ILM)

An ILM policy defines the phases an index will transition through and the actions performed in each phase . The main phases are:

```text
Hot Phase (current writes)      → 30GB or 1 day
├── Rollover to new index
Warm Phase (searchable)          → After 30 days
├── Shrink, force merge
Cold Phase (rarely accessed)      → After 90 days
├── Reduce replicas, freeze
Delete Phase                      → After 365 days
├── Remove indices
```

```json
PUT /_ilm/policy/logs_policy
{
  "policy": {
    "phases": {
      "hot": {
        "min_age": "0ms",
        "actions": {
          "rollover": {
            "max_primary_shard_size": "50gb",
            "max_age": "30d"
          },
          "set_priority": {
            "priority": 100
          }
        }
      },
      "warm": {
        "min_age": "30d",
        "actions": {
          "forcemerge": {
            "max_num_segments": 1
          },
          "allocate": {
            "number_of_replicas": 1,
            "require": {
              "data": "warm"
            }
          },
          "set_priority": {
            "priority": 50
          }
        }
      },
      "delete": {
        "min_age": "90d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```

## Common Time-Series Queries

Recent Errors:

```json
{
  "query": {
    "bool": {
      "must": [
        { "match": { "level": "error" } },
        { "range": { "@timestamp": { "gte": "now-1h" } } }
      ]
    }
  }
}
```

Aggregations Over Time:

```json
{
  "size": 0,
  "aggs": {
    "errors_over_time": {
      "date_histogram": {
        "field": "@timestamp",
        "interval": "hour"
      },
      "aggs": {
        "by_level": {
          "terms": { "field": "level" }
        },
        "avg_response_time": {
          "avg": { "field": "metrics.response_time" }
        }
      }
    }
  }
}
```

Percentiles for Performance:

```json
{
  "aggs": {
    "latency_percentiles": {
      "percentiles": {
        "field": "metrics.response_time",
        "percents": [50, 95, 99, 99.9]
      }
    }
  }
}
```

## Data Stream Pattern

<https://www.elastic.co/docs/manage-data/data-store/data-streams>

- Create an index lifecycle policy
- Create component templates
- Create an index template
- Create the data stream
