# Query DSL (Filters vs Queries)

- Queries: Calculate relevance scores (_score), affect ranking
- Flters: Yes/no decisions, cached, faster, no scoring

## Full-Text Queries:

```json
// Match (analyzes query text)
{ "match": { "field": "value" } }

// Match Phrase (exact phrase)
{ "match_phrase": { "field": "exact phrase" } }

// Multi-match (search multiple fields)
{ "multi_match": { "query": "value", "fields": ["field1", "field2"] } }
```

## Term-Level Queries:

```json
// Term (exact value, not analyzed)
{ "term": { "status": "active" } }

// Terms (multiple exact values)
{ "terms": { "status": ["active", "pending"] } }

// Range
{ "range": { "price": { "gte": 10, "lte": 100 } } }

// Exists (field exists)
{ "exists": { "field": "tags" } }
```

## Compound Queries:

```json
{
  "bool": {
    "must": [ { "match": { "title": "elasticsearch" } } ],
    "filter": [ { "term": { "status": "published" } } ],
    "should": [ { "match": { "content": "tutorial" } } ],
    "must_not": [ { "term": { "draft": true } } ]
  }
}
````

## Filter Context

```json
{
  "query": {
    "bool": {
      "filter": [              // Filters inside query context
        { "term": { "status": "active" } },
        { "range": { "price": { "lte": 100 } } }
      ],
      "must": [
        { "match": { "title": "search text" } }  // Actual query
      ]
    }
  }
}
```

## Nested

```json
{
  "query": {
    "nested": {
      "path": "items",
      "query": {
        "bool": {
          "must": [
            { "term": { "items.product": "Laptop" } },
            { "term": { "items.quantity": 1 } }
          ]
        }
      }
    }
  }
}
```

### Terms Aggregation with Count

```json
{
  "aggs": {
    "my_terms": {
      "terms": {
        "field": "field_name",
        "size": 10
      }
    }
  }
}
```
