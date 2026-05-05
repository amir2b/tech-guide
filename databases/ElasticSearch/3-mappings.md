# Mapping Design

Mapping defines how documents and their fields are stored and indexed.

## Field Data Types

<https://www.elastic.co/docs/reference/query-languages/sql/sql-data-types>

- **Numeric:** byte, short, integer, long, unsigned_long, half_float, float, scaled_float, double
- **String:** text, keyword
- **Date:** date
- **Boolean:** boolean
- **Location:** geo_point, geo_shape, shape
- **Complex:** nested, object

## Sample

```json
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text",           // Full-text search
        "fields": {
          "keyword": {             // Exact matching & sorting
            "type": "keyword",
            "ignore_above": 256
          }
        }
      },
      "price": {
        "type": "float"            // Numeric for range queries
      },
      "created_at": {
        "type": "date",            // Date for time-based queries
        "format": "yyyy-MM-dd"
      },
      "tags": {
        "type": "keyword"          // Array of exact values
      },
      "description": {
        "type": "text",
        "analyzer": "english"       // Language-specific analysis
      },
      "location": {
        "type": "geo_point"         // Geospatial queries
      }
    }
  }
}
```

## nested vs object

- nested: Arrays of objects that need independent querying
- object: Regular JSON objects

```json
{
  "mappings": {
    "properties": {
      "order_id": { "type": "keyword" },
      "customer": { 
        "type": "text",
        "fields": { "keyword": { "type": "keyword" } }
      },
      "items": {
        "type": "nested",
        "properties": {
          "product": { "type": "keyword" },
          "quantity": { "type": "integer" },
          "price": { "type": "float" }
        }
      },
      "store": {
        "type": "object",
        "properties": {
          "name": { "type": "text" },
          "code": { "type": "keyword" }
        }
      }
    }
  }
}
```

## CRUD

### Create a new Index

```text
PUT /users
{
    "mappings": {
        "properties": {
            ...
        }
    },
    "settings": {
        ...
    }
}
```

### Add a document to an Index

```text
POST /users/_doc
{
    "id": 1,
    "title": "Amir Bashiri"
}
```

### Query on Index

```text
GET /users/_search
{
    "query": {
        ...
    }
}
```

### Delete an Index

```text
DELETE users
```
