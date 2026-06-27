---
title: "LlamaIndex + Text-to-SQL: What I Learned"
date: 2025-10-27
tags: ["llamaindex", "text-to-sql", "llm", "sql", "ai agents", "langchain"]
---
## What I Was Trying to Do

I had structured data in SQLite and didn't want to write SQL for every question. **LlamaIndex** let me ask things like "which city has the most people?" in plain English — it figures out the query, runs it, and returns the answer.

Key capabilities include:

* Translating **natural language** into **SQL queries**.
* Converting **unstructured context** into structured insights.
* Dynamically retrieving **relevant schemas or tables** for complex databases.

> **Note:** Never expose full write-access databases to LLMs. Always use **read-only credentials** or **sandboxed environments** for safety.

---

## Setup: Creating a Sample Database

Let’s begin with a simple in-memory SQLite database using SQLAlchemy:

```python
from sqlalchemy import create_engine, MetaData, Table, Column, String, Integer

engine = create_engine("sqlite:///:memory:")
metadata_obj = MetaData()

city_stats_table = Table(
    "city_stats",
    metadata_obj,
    Column("city_name", String(16), primary_key=True),
    Column("population", Integer),
    Column("country", String(16), nullable=False),
)
metadata_obj.create_all(engine)
```

Add some sample rows:

```python
from sqlalchemy import insert

rows = [
    {"city_name": "Toronto", "population": 2731571, "country": "Canada"},
    {"city_name": "Tokyo", "population": 13929286, "country": "Japan"},
    {"city_name": "Berlin", "population": 600000, "country": "Germany"},
]

for row in rows:
    stmt = insert(city_stats_table).values(**row)
    with engine.begin() as connection:
        connection.execute(stmt)
```

---

## Connecting with LlamaIndex

Now, we’ll connect this database to LlamaIndex using its **SQLDatabase** wrapper:

```python
from llama_index.core import SQLDatabase

sql_database = SQLDatabase(engine, include_tables=["city_stats"])
```

This wrapper allows LlamaIndex to inspect the database schema and use it for reasoning during query generation.

---

## Ask Questions in Natural Language

LlamaIndex provides the **NLSQLTableQueryEngine**, which takes a question in plain English and automatically converts it to SQL:

```python
from llama_index.llms.ollama import Ollama
from llama_index.embeddings.ollama import OllamaEmbedding
from llama_index.core import Settings
from llama_index.core.indices.struct_store import NLSQLTableQueryEngine

llm = Ollama(model="llama3", request_timeout=120)
embed_model = OllamaEmbedding(model_name="nomic-embed-text")

Settings.llm = llm
Settings.embed_model = embed_model

query_engine = NLSQLTableQueryEngine(
    sql_database=sql_database,
    tables=["city_stats"],
    llm=llm,
    embed_model=embed_model,
)
response = query_engine.query("Which city has the highest population?")
```

**Output:**

> Tokyo has the highest population.

This approach is perfect when your database has a small number of known tables.

---

## Creating a Table Schema Index

For larger databases or dynamic querying, you can create a **schema index** to help the model understand table structures semantically:

```python
from llama_index.core import VectorStoreIndex
from llama_index.core.objects import SQLTableNodeMapping, ObjectIndex, SQLTableSchema

table_node_mapping = SQLTableNodeMapping(sql_database)
table_schema_objs = [SQLTableSchema(table_name="city_stats")]

obj_index = ObjectIndex.from_objects(
    table_schema_objs,
    table_node_mapping,
    VectorStoreIndex,
)
```

This allows LlamaIndex to retrieve relevant table schemas automatically during queries.

---

## Schema-Aware Querying

When multiple tables exist, you can use the **SQLTableRetrieverQueryEngine** to pick the best one:

```python
from llama_index.core.indices.struct_store import SQLTableRetrieverQueryEngine

query_engine = SQLTableRetrieverQueryEngine(
    sql_database, obj_index.as_retriever(similarity_top_k=1)
)
response = query_engine.query("Which city has the highest population?")
```

> **Result:** Tokyo has the highest population.

---

## Improving Query Accuracy

### 1. Row-Level Embeddings

You can embed individual rows to allow **semantic row retrieval**:

```python
from llama_index.core.schema import TextNode

with engine.connect() as connection:
    results = connection.execute(stmt).fetchall()

city_nodes = [TextNode(text=str(t)) for t in results]
city_rows_index = VectorStoreIndex(city_nodes, embed_model=embed_model)
city_rows_retriever = city_rows_index.as_retriever(similarity_top_k=1)
```

This helps for ambiguous queries like:

> How many cities belong to a specific country?”

### 2. Column-Level Embeddings

Instead of indexing full rows, you can embed **distinct column values** for better efficiency:

```python
for column_name in ["city_name", "country"]:
    stmt = select(city_stats_table.c[column_name]).distinct()
    ...
```

---

## Using the Retriever Directly

For more flexibility, use the **NLSQLRetriever** directly:

```python
from llama_index.core.retrievers import NLSQLRetriever

nl_sql_retriever = NLSQLRetriever(
    sql_database, tables=["city_stats"], llm=llm, return_raw=True
)
```

Or pair it with a query engine:

```python
from llama_index.core.query_engine import RetrieverQueryEngine

query_engine = RetrieverQueryEngine.from_args(nl_sql_retriever)
response = query_engine.query(
    "Return the top 5 cities with the highest population."
)
```

---

## Summary

| Component                        | Purpose                                            |
| -------------------------------- | -------------------------------------------------- |
| **SQLDatabase**                  | Wraps SQLAlchemy engine for LlamaIndex integration |
| **NLSQLTableQueryEngine**        | Converts natural language into SQL queries         |
| **ObjectIndex**                  | Stores table schemas for retrieval                 |
| **SQLTableRetrieverQueryEngine** | Combines schema retrieval and SQL generation       |
| **Row/Column Retrievers**        | Enhance accuracy using semantic similarity         |

---

## Key Takeaways

* LlamaIndex enables conversational querying over structured databases.
* Supports schema understanding, reasoning, and contextual retrieval.
* Can dynamically select relevant tables, rows, and columns during inference.
* Useful for building **smart analytics dashboards**, **internal assistants**, or **AI-powered data explorers**.

---

**References:**

* [LlamaIndex Documentation](https://docs.llamaindex.ai)
* [GitHub Repository](https://github.com/run-llama/llama_index)
