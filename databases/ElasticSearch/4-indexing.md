# Analyzers, Tokenizers, and Normalization

## What is an Inverted Index?

An inverted index is the core data structure that makes Elasticsearch fast for text search. Think of it like a book's index:

### Traditional (Forward) Index

- Document 1: "The quick brown fox"
- Document 2: "The lazy dog"
- Document 3: "Brown bears are quick"

### Inverted Index

- "the" → Doc1, Doc2
- "quick" → Doc1, Doc3
- "brown" → Doc1, Doc3
- "fox" → Doc1
- "lazy" → Doc2
- "dog" → Doc2
- "bears" → Doc3
- "are" → Doc3

## How Full-Text Search Works

1. **Tokenization:** Text is broken into individual terms (tokens)
2. **Normalization:** Terms are normalized (lowercasing, stemming, etc.)
3. **Indexing:** Terms are mapped to document IDs with positions
4. **Searching:** Query terms are looked up in the inverted index
5. **Relevance Scoring:** Results are ranked using algorithms like TF-IDF or BM25

### Normalization Techniques

- Lowercasing: Convert all text to lowercase
- Stemming: Reduce words to root form (running → run)
- Lemmatization: Contextual root form (better → good)
- Stop Words: Remove common words (the, a, an)
- Synonyms: Map equivalent terms
- ASCII Folding: Convert accents (café → cafe)

## Analyzer Components

<https://www.elastic.co/docs/manage-data/data-store/text-analysis/anatomy-of-an-analyzer>

An analyzer is a package which contains three lower-level building blocks:

1. Character Filters (optional): Preprocess characters
2. Tokenizer: Splits text into tokens
3. Token Filters (optional): Modify tokens

### Common Analyzers

- **Standard Analyzer:** Default, splits on word boundaries
- **Simple Analyzer:** Splits on non-letters, lowercases
- **Whitespace Analyzer:** Splits on whitespace only
- **Keyword Analyzer:** No analysis (entire field as one token)
- **Language Analyzers:** English, French, etc. (stemming, stop words)

```json
GET _analyze
{
  "analyzer": "standard",
  "text": "I'll be at the café at 2pm!"
}
// Output tokens: ["i'll", "be", "at", "the", "café", "at", "2pm"]

GET _analyze
{
  "analyzer": "whitespace",
  "text":     "The quick brown fox."
}
// Output tokens: ["The", "quick", "brown", "fox"]

GET /_analyze
{
  "tokenizer": "standard",
  "filter": ["lowercase", "porter_stem"],
  "text": "the foxes jumping quickly"
}
// Output tokens: ["the", "fox", "jump", "quickli"]
```

```json
PUT users
{
  "mappings": {
    "properties": {
      "description": {
        "type": "text",
        "analyzer": "persian"
      }
    }
  }
}

GET users/_analyze
{
  "field": "bio",
  "text": "امیر بشیری در کشور از ایران. میتواند بماند"
}
```

### Custom Analyzer Example

<https://www.elastic.co/docs/reference/elasticsearch/mapping-reference/analyzer>

```json
PUT users
{
  "mappings": {
    "properties": {
      "bio": {
        "type": "text",
        "analyzer": "my_analyzer"
      }
    }
  },
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "type": "custom",
          "char_filter": ["my_char_filter", "html_strip"],
          "tokenizer": "my_tokenizer",
          "filter": ["lowercase", "my_filter", "porter_stem"]
        }
      },
      "char_filter": {
        "my_char_filter": {
          "type": "mapping",
          "mappings": ["& => and"]
        }
      },
      "tokenizer": {
        "my_tokenizer": {
          "type": "pattern",
          "pattern": "[ ,.!?]+"
        }
      },
      "filter": {
        "my_filter": {
          "type": "stop",
          "stopwords": ["_english_"]
        }
      }
    }
  }
}
```

<https://www.elastic.co/docs/reference/text-analysis/analysis-lang-analyzer#persian-analyzer>
