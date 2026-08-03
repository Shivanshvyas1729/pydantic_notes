<details><summary>fast track</summary>

<img width="1408" height="768" alt="Gemini_Generated_Image_43ubmj43ubmj43ub" src="https://github.com/user-attachments/assets/e2efcd27-ff77-4015-a563-967d2a20a90e" />

-----------------------
<img width="1408" height="768" alt="Gemini_Generated_Image_iiac8biiac8biiac" src="https://github.com/user-attachments/assets/84f2fdfd-8be3-4cdc-afef-58f748ef59b8" />

-------------

<img width="768" height="1376" alt="Gemini_Generated_Image_l4turll4turll4tu" src="https://github.com/user-attachments/assets/edf47c17-d29e-4bd0-aa54-98b3d6d0ad6a" />

-----------------------
<img width="768" height="1376" alt="Gemini_Generated_Image_whstniwhstniwhst" src="https://github.com/user-attachments/assets/e62264f5-4a53-4d4c-906e-50b998146891" />

------------------------
<img width="768" height="1376" alt="Gemini_Generated_Image_dcah9sdcah9sdcah" src="https://github.com/user-attachments/assets/e7681233-323a-4438-a36c-495aade7a165" />

----------------------------

<img width="768" height="1376" alt="Gemini_Generated_Image_qmrce4qmrce4qmrc" src="https://github.com/user-attachments/assets/9e3059a7-6287-4917-be5d-3db085f4e19b" />

---------------------------
<img width="768" height="1376" alt="Gemini_Generated_Image_hl3jtahl3jtahl3j" src="https://github.com/user-attachments/assets/e079e10c-7521-4f03-a4ae-0b12cd58673f" />

































</details>

<details><summary>basic code to check connection is working or not</summary>

import asyncio
from pymongo import AsyncMongoClient

async def main():
    uri = "<connection string URI>"
    client = AsyncMongoClient(uri)

    try:
        database = client.get_database("sample_mflix")
        movies = database.get_collection("movies")

        # Query for a movie that has the title 'Back to the Future'
        query = { "title": "Back to the Future" }
        movie = await movies.find_one(query)

        print(movie)

        await client.close()

    except Exception as e:
        raise Exception("Unable to find the document due to the following error: ", e)

# Run the async function
asyncio.run(main())


</details>

<details><summary>basics</summary>
# The Ultimate MongoDB & PyMongo Masterclass Guide (v4.15+)

## Table of Contents
1. [Part 1 — MongoDB Fundamentals](#part-1--mongodb-fundamentals)
2. [Part 2 — CRUD Operations](#part-2--crud-operations)
3. [Part 3A — Querying & Operators](#part-3a--querying--operators)
4. [Part 3B — Data Retrieval & Pagination](#part-3b--data-retrieval--pagination)
5. [Part 4 — Aggregation Pipeline](#part-4--aggregation-pipeline)
6. [Part 5 — Indexes & Performance](#part-5--indexes--performance)
7. [Part 6 — Transactions, ACID & Data Safety](#part-6--transactions-acid--data-safety)
8. [Part 7 — Schema Design & Validation](#part-7--schema-design--validation)
9. [Part 8 — Advanced MongoDB](#part-8--advanced-mongodb)
10. [Part 9 — MongoDB as a Vector Database (Atlas Vector Search)](#part-9--mongodb-as-a-vector-database-atlas-vector-search)
11. [Part 10 — Production RAG Pipeline (Sync & Async)](#part-10--production-rag-pipeline-sync--async)

---

## Part 1 — MongoDB Fundamentals

### 1.1 What is MongoDB?
**Intuitive Analogy (Spreadsheet vs. Filing Cabinet):**
Imagine managing an e-commerce catalog.
*   **SQL:** A rigid Excel workbook where every sheet has pre-defined columns. If a product needs a new attribute like `shoe_size`, you must add a column to the entire table, creating thousands of empty `NULL` cells for non-shoe items like laptops or books.
*   **MongoDB:** A modern filing cabinet. Each product gets its own folder (document). A shoe document contains `shoe_size`, while a laptop document contains `ram_gb` and `cpu`. They store peacefully side-by-side in the same collection without forcing empty padding or table schema migrations.

**Official Definition:** MongoDB is an open-source, document-oriented NoSQL database built for dynamic schema flexibility, horizontal scale-out architecture, and high write/read throughput.

### 1.2 SQL vs NoSQL
| Feature | SQL (PostgreSQL, MySQL) | NoSQL (MongoDB) |
| :--- | :--- | :--- |
| **Data Model** | Relational tables (Rows & Columns) | Flexible BSON Documents |
| **Schema** | Strict, pre-defined upfront | Dynamic / Flexible |
| **Scaling** | Vertical (Bigger CPU/RAM) | Horizontal (Sharding across nodes) |
| **Joins** | Server-side foreign key JOINs | Embedded documents or `$lookup` |
| **Primary Goal** | Rigid transactional normalization | High write/read throughput, schema evolution, scale |

### 1.3 BSON (Binary JSON)
**Why plain JSON is insufficient for databases:**
Plain text JSON is human-readable, but has two core drawbacks:
1.  **Slow Parsing:** Finding a field inside text JSON requires string scanning ($O(N)$ overhead).
2.  **Type Limitations:** JSON only supports String, Number, Boolean, Array, Object, and Null. It lacks native types for precise dates, 64-bit integers, binary blobs, and floating-point precision.

**How BSON Solves This:**
MongoDB converts JSON into BSON (Binary JSON) internally.
*   **Length Prefixes:** BSON prefixes every field with its byte size, allowing MongoDB to jump directly to specific fields in memory without parsing preceding data.
*   **Rich Data Types:** Includes native types like `ObjectId`, `Date`, `BinData`, `Decimal128`, `32-bit Int`, and `64-bit Long`.

### 1.4 Documents, Collections, and Databases
```text
Database ("company_db")
  └── Collection ("employees")
        ├── Document 1 (BSON) -> {"_id": ..., "name": "Sarah"}
        ├── Document 2 (BSON) -> {"_id": ..., "name": "Aarav"}
        └── Document 3 (BSON) -> {"_id": ..., "name": "John"}
```
*   **Document:** The fundamental unit of data in MongoDB (equivalent to a row in SQL). Maximum BSON document size: 16 MB.
*   **Collection:** A logical grouping of documents (equivalent to a table in SQL).
*   **Database:** A logical container holding multiple collections.

### 1.5 Cluster, Replica Sets, and Sharding
*   **Cluster:** A set of network nodes hosting your MongoDB deployment.
*   **Replica Set:** A primary-secondary cluster group providing high availability and automatic failover.
    *   **Primary Node:** Handles all write operations and default read operations.
    *   **Secondary Nodes:** Replicate the primary's operations log (oplog) asynchronously. If the primary fails, an automated election promotes a secondary to primary.
*   **Sharding:** Distributes massive datasets across multiple machine clusters using a Shard Key to achieve horizontal scaling.

### 1.6 MongoDB Atlas & Connection URI
**MongoDB Connection URI Format:**
```text
mongodb+srv://<username>:<password>@cluster0.abcde.mongodb.net/?retryWrites=true&w=majority
```
*   `mongodb+srv://`: Uses DNS seedlists to discover topology changes dynamically without updating connection strings in application code.

### 1.7 Sync (MongoClient) vs Async (AsyncMongoClient)
**The Restaurant Waiter Analogy:**
*   **Synchronous (MongoClient):** A waiter who takes an order, stands idle at the kitchen window waiting for the chef to finish, delivers the food, and only then attends to the next customer.
*   **Asynchronous (AsyncMongoClient):** A waiter who submits an order to the kitchen and immediately takes orders from other tables. When a dish is ready, the waiter pauses briefly to deliver it.

**PyMongo Execution Model:**
Python database operations are Network I/O bound.
*   In **WSGI** applications (Django, Flask), standard `MongoClient` blocks the thread during network calls.
*   In **ASGI** high-concurrency frameworks (FastAPI, Starlette), `AsyncMongoClient` releases control to Python's `asyncio` Event Loop during I/O wait times using `await`.

---

## Part 2 — CRUD Operations

### 2.1 Client Connection Setup

**Synchronous Setup (MongoClient)**
```python
from pymongo import MongoClient

# Instantiate standard client
client = MongoClient("mongodb://localhost:27017/", maxPoolSize=50)

# Access Database & Collection
db = client["company_db"]
employees = db["employees"]
```

**Asynchronous Setup (AsyncMongoClient - PyMongo 4.15+)**
```python
import asyncio
from pymongo import AsyncMongoClient

async def main():
    # Instantiate Async Client
    async_client = AsyncMongoClient("mongodb://localhost:27017/")
    async_db = async_client["company_db"]
    async_employees = async_db["employees"]
    
    # Non-blocking async operation
    doc = await async_employees.find_one({"department": "AI"})
    print(doc)
    
    await async_client.close()

# asyncio.run(main())
```

### 2.2 Create Operations (insert_one, insert_many)
```python
# Sync Example
result_one = employees.insert_one({
    "name": "Sarah Connor",
    "department": "Engineering",
    "salary": 95000,
    "skills": ["Python", "Docker"]
})
print(f"Inserted Document ID: {result_one.inserted_id}")

# Bulk Insert
result_many = employees.insert_many([
    {"name": "John Doe", "department": "AI", "salary": 110000},
    {"name": "Alice Smith", "department": "AI", "salary": 125000}
], ordered=True) # ordered=True halts on first failure; ordered=False continues valid inserts

# Async Equivalent
result_one = await async_employees.insert_one({"name": "Sarah Connor", "department": "Engineering"})
```

### 2.3 Read Operations (find, find_one)
```python
# find_one returns a single dict or None
emp = employees.find_one({"name": "Sarah Connor"})

# find returns a Cursor (iterable)
cursor = employees.find({"department": "AI"})

# Sync Loop
for doc in cursor:
    print(doc["name"], doc["salary"])

# Async Cursor Loop
async_cursor = async_employees.find({"department": "AI"})
async for doc in async_cursor:
    print(doc["name"], doc["salary"])
```

### 2.4 Update Operations (update_one, update_many, replace_one)
⚠️ **Beginner Pitfall:** Always use update operators like `$set` or `$inc`! Passing `{"salary": 100000}` directly into an `update_one` query without `$set` will raise a ValueError in modern PyMongo.

```python
# Field update with $set and array push with $push
employees.update_one(
    {"name": "Sarah Connor"},
    {"$set": {"salary": 100000}, "$push": {"skills": "MongoDB"}}
)

# Bulk increment
employees.update_many(
    {"department": "AI"},
    {"$inc": {"salary": 5000}} # Increment salary by 5000
)

# Replace entire document (preserves original _id)
employees.replace_one(
    {"name": "John Doe"},
    {"name": "John Doe", "department": "Data Science", "salary": 115000}
)
```

### 2.5 Delete Operations (delete_one, delete_many)
```python
# Delete single matching document
employees.delete_one({"name": "John Doe"})

# Delete multiple documents
result = employees.delete_many({"department": "Legacy"})
print(f"Deleted Count: {result.deleted_count}")
```

### 2.6 Document Counting
```python
# Filtered count (Scans index or collection)
ai_count = employees.count_documents({"department": "AI"})

# Total metadata estimate (Fast check from collection stats, filters NOT allowed)
total_estimate = employees.estimated_document_count()
```
⚠️ **Performance Rule:** Never use `len(list(collection.find()))` to count documents! It loads all matching documents over the network into Python memory, causing severe slowdowns.

### 2.7 Best Practices for Connections
**Reuse Client Instances:** MongoClient manages its own internal connection pool. Instantiate one global client object per application process. Do not create and close clients inside individual request handlers or loop iterations!

---

## Part 3A — Querying & Operators

### 3.1 Query Execution Flow & Implicit AND
When multiple key-value pairs are passed into a query dictionary, MongoDB evaluates them using Implicit AND logic:
```python
# Implicit AND: Matches department == "AI" AND salary >= 90000
employees.find({
    "department": "AI",
    "salary": {"$gte": 90000}
})
```

### 3.2 Comparison Operators
| Operator | Meaning | PyMongo Example |
| :--- | :--- | :--- |
| `$eq` | Equal to | `{"age": {"$eq": 30}}` |
| `$ne` | Not equal to | `{"status": {"$ne": "Inactive"}}` |
| `$gt` / `$gte` | Greater than / or equal | `{"salary": {"$gte": 90000}}` |
| `$lt` / `$lte` | Less than / or equal | `{"age": {"$lt": 40}}` |

### 3.3 Logical Operators ($and, $or, $not, $nor)
```python
# $or condition
employees.find({
    "$or": [
        {"department": "AI"},
        {"salary": {"$gt": 120000}}
    ]
})

# Explicit $and is required when querying the same field multiple times
employees.find({
    "$and": [
        {"salary": {"$gt": 50000}},
        {"salary": {"$lt": 100000}}
    ]
})
```

### 3.4 Array Operators
```python
# $in: Matches any value present in the filter array
employees.find({"department": {"$in": ["AI", "Engineering"]}})

# $all: Matches documents where the array contains ALL specified elements
employees.find({"skills": {"$all": ["Python", "MongoDB"]}})

# $size: Matches array with exact element count
employees.find({"skills": {"$size": 3}})

# $elemMatch: Matches embedded objects meeting ALL sub-criteria simultaneously
employees.find({
    "projects": {
        "$elemMatch": {"title": "Apollo", "status": "Completed"}
    }
})
```

### 3.5 Element & Regex Operators
```python
# Check field presence
employees.find({"bonus": {"$exists": True}})

# Type checking (e.g., "string", "int", "double", "array")
employees.find({"age": {"$type": "int"}})

# Regular Expression Search
employees.find({"name": {"$regex": "^Sarah", "$options": "i"}})
```

### 3.6 Dot Notation for Embedded Documents
Use string path dot notation to query nested sub-documents:
```python
employees.find({"address.city": "Ajmer"})
```

---

## Part 3B — Data Retrieval & Pagination

### 3.1 Projection (Selecting Specific Fields)
Projection reduces network payload by forcing MongoDB to return only requested fields.
```python
# Include 'name' and 'age', explicitly exclude '_id'
employees.find(
    {"department": "AI"},
    {"name": 1, "age": 1, "_id": 0}
)
```
⚠️ **Rule:** You cannot mix inclusion (1) and exclusion (0) in a single projection, except for the `_id` field.

### 3.2 Sorting, Skip, Limit & Pagination
```python
# Single Field Sort: 1 = Ascending, -1 = Descending
employees.find().sort("salary", -1)

# Compound Field Sort
employees.find().sort([
    ("department", 1),
    ("salary", -1)
])

# Offset Pagination Formula: Page N with size PageSize
# cursor.sort().skip((N - 1) * PageSize).limit(PageSize)
page_num = 2
page_size = 10

employees.find()    .sort("salary", -1)    .skip((page_num - 1) * page_size)    .limit(page_size)
```

### 3.3 The Cursor Mental Model & Batch Fetching
Calling `employees.find()` does NOT transfer matching documents across the network immediately.
1.  `find()` creates a stateful Cursor pointer on the server.
2.  When iteration begins (`for doc in cursor:`), MongoDB transmits the first batch (101 documents or 1 MB max).
3.  As Python consumes local items, PyMongo transparently triggers `getMore` network requests behind the scenes for remaining batches.

---

## Part 4 — Aggregation Pipeline

### 4.1 The Factory Assembly Line Analogy
*   `find()` is a warehouse retrieval operation: It fetches raw documents matching filters directly.
*   `aggregate()` is an industrial assembly line:
    *   **Stage 1 ($match):** Discards unwanted raw materials.
    *   **Stage 2 ($group):** Melts items down and calculates aggregates.
    *   **Stage 3 ($project):** Stamps labels and formats the final result documents.

`[ Collection ] ──> [$match] ──> [$group] ──> [$project] ──> [ Final Results ]`

### 4.2 Essential Pipeline Stages & Code Example
```python
pipeline = [
    # Stage 1: Filter active employees in AI
    {"$match": {"department": "AI", "status": "Active"}},
    
    # Stage 2: Group by department and calculate averages
    {
        "$group": {
            "_id": "$department",
            "avgSalary": {"$avg": "$salary"},
            "totalHeadcount": {"$sum": 1}
        }
    },
    
    # Stage 3: Reshape output
    {
        "$project": {
            "_id": 0,
            "department": "$_id",
            "avgSalary": {"$round": ["$avgSalary", 2]},
            "totalHeadcount": 1
        }
    },
    
    # Stage 4: Sort
    {"$sort": {"avgSalary": -1}}
]

results = employees.aggregate(pipeline)
for record in results:
    print(record)
```

### 4.3 Accumulators & Array Deconstruction
*   **Group Accumulators:** `$sum`, `$avg`, `$min`, `$max`, `$first`, `$last`, `$push`, `$addToSet`.
*   **$unwind:** Flattens array fields, creating one output document per array element.
*   **$lookup (Left Outer Join):**
```python
employees.aggregate([
    {
        "$lookup": {
            "from": "departments",          # Target collection
            "localField": "department_id",  # Field in employees
            "foreignField": "_id",          # Field in departments
            "as": "department_info"         # Output array attribute
        }
    }
])
```

---

## Part 5 — Indexes & Performance

### 5.1 What is an Index?
Without an index, MongoDB performs a `COLLSCAN` (Collection Scan), reading every document on disk sequentially ($O(N)$ overhead).
An Index builds an ordered B-Tree structure in RAM that points directly to document locations on disk, reducing search complexity to $O(\log N)$ (`IXSCAN`).

### 5.2 Types of Indexes
*   **Single Field Index:** `employees.create_index([("email", 1)], unique=True)`
*   **Compound Index:** `employees.create_index([("department", 1), ("salary", -1)])`
*   **TTL (Time-To-Live) Index:** Automatically removes documents after a specified time window: `sessions.create_index([("createdAt", 1)], expireAfterSeconds=3600)`
*   **Multikey Index:** Automatically created when indexing an array field.
*   **Partial Index:** Indexes only documents matching a specific expression (saves RAM).
*   **Wildcard Index:** Indexes arbitrary nested fields in dynamic schemas.

### 5.3 The ESR Rule for Compound Indexes
When building multi-field indexes, order fields by **Equality, Sort, Range**:
1.  **E - Equality:** Exact match fields first (`{"department": "AI"}`).
2.  **S - Sort:** Ordering fields second (`sort([("salary", -1)])`).
3.  **R - Range:** Range filters last (`{"age": {"$gte": 25}}`).

### 5.4 Query Diagnostics: explain()
```python
explanation = employees.find({"department": "AI"}).explain("executionStats")
print(explanation["executionStats"]["executionStages"])
```
*   `COLLSCAN`: Bad! Full table scan.
*   `IXSCAN`: Good! Used an index B-Tree.
*   `COVERED QUERY (PROSTAGE_IXSCAN)`: Ideal! Query was fulfilled entirely out of RAM index memory without reading underlying documents on disk.

---

## Part 6 — Transactions, ACID & Data Safety

### 6.1 What is ACID?
ACID represents the core guarantees that a database makes to keep data accurate and reliable.

```text
       ┌─────────────────────────────────────────┐
       │             A C I D                     │
       ├───────────┬─────────────┬───────────────┼─────────────┐
       │ Atomicity │ Consistency │   Isolation   │ Durability  │
       │  (All or  │  (Rules are │  (No peeking  │   (Saved    │
       │  Nothing) │   obeyed)   │  in-flight)   │  forever)   │
       └───────────┴─────────────┴───────────────┴─────────────┘
```
*   **A — Atomicity ("All or Nothing"):** Either every operation inside a unit of work succeeds, or none do. (MongoDB natively guarantees single-document atomicity).
*   **C — Consistency ("Valid State"):** Data must always satisfy defined schema constraints and unique indexes.
*   **I — Isolation ("Invisible In-Flight"):** Transactions executing concurrently cannot see each other's uncommitted or intermediate states.
*   **D — Durability ("Saved Forever"):** Committed changes are permanently recorded via the WiredTiger Journal.

### 6.2 Multi-Document Transactions & Sessions in PyMongo
In PyMongo, transactions must run inside a Session (`ClientSession`).

**Synchronous Example**
```python
from pymongo import MongoClient
client = MongoClient("mongodb://localhost:27017/")

with client.start_session() as session:
    with session.start_transaction():
        db = client["bank"]
        db.accounts.update_one({"name": "Alice"}, {"$inc": {"balance": -100}}, session=session)
        db.accounts.update_one({"name": "Bob"}, {"$inc": {"balance": 100}}, session=session)
```

**Asynchronous Example**
```python
import asyncio
from pymongo import AsyncMongoClient

async def transfer_funds():
    client = AsyncMongoClient("mongodb://localhost:27017/")
    async with await client.start_session() as session:
        async with session.start_transaction():
            db = client["bank"]
            await db.accounts.update_one({"name": "Alice"}, {"$inc": {"balance": -100}}, session=session)
            await db.accounts.update_one({"name": "Bob"}, {"$inc": {"balance": 100}}, session=session)
```

💡 **Interview Tip:** "In MongoDB, do you always need transactions?" No! Most operations should be modeled using embedded documents, updating atomically in a single document call without transaction overhead.

### 6.3 Read & Write Concerns (Data Safety)
*   **Write Concern (w):**
    *   `w: 1` (Default): Acknowledged as soon as Primary writes to memory.
    *   `w: "majority"`: Acknowledged only after a majority of nodes write to logs.
    *   `w: 0`: "Fire and forget." Unacknowledged writes.
*   **Read Concern:** `local` (fastest), `majority` (safest, no rollback), `snapshot` (used in transactions).

### 6.4 Bulk Operations
```python
from pymongo import InsertOne, UpdateOne, DeleteOne
ops = [
    InsertOne({"name": "David", "department": "AI"}),
    UpdateOne({"name": "Sarah"}, {"$inc": {"salary": 10000}}),
    DeleteOne({"status": "Terminated"})
]
# Send batch write operation over network in a single payload
result = employees.bulk_write(ops, ordered=False)
```

---

## Part 7 — Schema Design & Validation

### 7.1 Embedding vs Referencing
**General Rule:** "Data accessed together should be stored together."

*   **Embedding (Denormalized)**: Place child items directly inside an array or sub-document.
    *   **Best For:** 1-to-Few relationships, data read together frequently.
    *   **Pros:** Single query retrieval ($O(1)$ lookup), atomic updates.
    *   **Cons:** Hard 16 MB limit; document bloat risk.
*   **Referencing (Normalized)**: Store `ObjectId` links pointing to documents in another collection.
    *   **Best For:** 1-to-Many or Many-to-Many relationships, unbounded datasets (e.g., logs).
    *   **Pros:** Bypasses 16 MB limit, avoids data duplication.
    *   **Cons:** Requires `$lookup` (joins) or multiple application queries.

### 7.2 Document Schema Validation
Enforce schema rules directly inside MongoDB using JSON Schema:
```python
db.create_collection("users", validator={
    "$jsonSchema": {
        "bsonType": "object",
        "required": ["name", "email", "age"],
        "properties": {
            "name": {"bsonType": "string"},
            "email": {"bsonType": "string", "pattern": "^.+@.+$"},
            "age": {"bsonType": "int", "minimum": 18}
        }
    }
})
```

---

## Part 8 — Advanced MongoDB

### 8.1 GridFS (Large File Storage)
Files exceeding the 16 MB document limit are chunked into smaller documents using GridFS.
```python
import gridfs
fs = gridfs.GridFS(db)

# Store binary file
file_id = fs.put(b"Binary content bytes...", filename="report.pdf")

# Retrieve binary file
file_bytes = fs.get(file_id).read()
```

### 8.2 Change Streams (Real-Time Reactive Events)
Listen to real-time database modifications on Replica Sets:
```python
with db.employees.watch() as stream:
    for change in stream:
        print(f"Change Event Type: {change['operationType']}")
        print(f"Updated Document ID: {change['documentKey']}")
```

### 8.3 Specialized Collections
*   **Time Series Collections:** Optimized for temporal measurements, IoT, and financial ticks.
*   **Capped Collections:** Fixed-size circular buffer collections that automatically drop oldest documents when the limit is reached.

---

## Part 9 — MongoDB as a Vector Database (Atlas Vector Search)

### 9.1 The "Synchronization Tax" Problem
Traditional AI architectures store operational data in MongoDB and embeddings in a standalone vector database (Pinecone/Milvus), creating synchronization overhead, data stale risks, and extra latency. MongoDB Atlas Vector Search natively integrates vector capabilities directly into the BSON document model.

### 9.2 Similarity Metrics in MongoDB
When querying vectors, MongoDB computes distance between Query Vector ($q$) and Document Vector ($u$):

| Metric | Formula | Range | Best Used For |
| :--- | :--- | :--- | :--- |
| **Cosine** | $\cos(	heta) = rac{q \cdot u}{\|q\| \|u\|}$ | $[-1, 1]$ | Text similarity when vector length varies. |
| **Euclidean** | $d(q, u) = \sqrt{\sum (q_i - u_i)^2}$ | $[0, \infty)$ | Audio, visual spatial queries. |
| **Dot Product** | $q \cdot u = \sum q_i u_i$ | $(-\infty, \infty)$ | Normalized vectors ($\|v\| = 1$). Fastest! |

### 9.3 Indexing Vectors (HNSW Algorithm)
To avoid slow Exact Nearest Neighbors ($O(N \cdot d)$), MongoDB uses **HNSW** (Hierarchical Navigable Small World) graphs to perform Approximate Nearest Neighbors (ANN) in $O(\log N)$ time.

**Index Definition JSON:**
```json
{
  "fields": [
    {
      "type": "vector",
      "path": "embedding",
      "numDimensions": 1536,
      "similarity": "cosine"
    },
    {
      "type": "filter",
      "path": "category"
    }
  ]
}
```

### 9.4 The $vectorSearch Aggregation Stage
```python
pipeline = [
    {
        "$vectorSearch": {
            "index": "vector_index_name",
            "path": "embedding",
            "queryVector": [0.012, -0.04, ...],
            "numCandidates": 100,  # Priority queue size (10x-20x the limit)
            "limit": 5,
            "filter": {"category": "AI"}  # Pre-filtering
        }
    }
]
```

### 9.5 Filtering Strategies & Score Projection
*   **Pre-filtering (Inside `$vectorSearch`):** Evaluates metadata *before* vector graph traversal (Highly Efficient).
*   **Post-filtering (`$match` after `$vectorSearch`):** Scans the whole vector index first, then discards non-matching documents (Inefficient for narrow scopes).
*   **Score Projection:** Retrieve normalized similarity scores using `{"$meta": "vectorSearchScore"}` in a `$project` stage.

---

## Part 10 — Production RAG Pipeline (Sync & Async)

### 10.1 Synchronous RAG Pipeline (MongoClient)
```python
import os
from pymongo import MongoClient
from openai import OpenAI

client = MongoClient("mongodb+srv://<username>:<password>@cluster.mongodb.net/")
db = client["knowledge_base"]
collection = db["documents"]
ai_client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

def vector_search_rag(user_query: str):
    # 1. Embed query
    response = ai_client.embeddings.create(model="text-embedding-3-small", input=user_query)
    query_vector = response.data[0].embedding

    # 2. Vector Search Pipeline
    pipeline = [
        {
            "$vectorSearch": {
                "index": "vector_index",
                "path": "embedding",
                "queryVector": query_vector,
                "numCandidates": 100,
                "limit": 3
            }
        },
        {"$project": {"_id": 0, "content": 1, "score": {"$meta": "vectorSearchScore"}}}
    ]

    # 3. Retrieve Context
    results = list(collection.aggregate(pipeline))
    context_str = "
---
".join([doc["content"] for doc in results])

    # 4. Generate LLM Output
    prompt = f"Context:
{context_str}

User Query: {user_query}
Answer:"
    completion = ai_client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": prompt}]
    )
    return completion.choices[0].message.content
```

### 10.2 Asynchronous RAG Pipeline (AsyncMongoClient)
```python
import asyncio, os
from pymongo import AsyncMongoClient
from openai import AsyncOpenAI

async def async_vector_search_rag(user_query: str):
    mongo_client = AsyncMongoClient("mongodb+srv://<username>:<password>@cluster.mongodb.net/")
    collection = mongo_client["knowledge_base"]["documents"]
    ai_client = AsyncOpenAI(api_key=os.getenv("OPENAI_API_KEY"))

    response = await ai_client.embeddings.create(model="text-embedding-3-small", input=user_query)
    query_vector = response.data[0].embedding

    pipeline = [
        {
            "$vectorSearch": {
                "index": "vector_index", "path": "embedding",
                "queryVector": query_vector, "numCandidates": 100, "limit": 3
            }
        },
        {"$project": {"content": 1}}
    ]

    cursor = await collection.aggregate(pipeline)
    retrieved_docs = await cursor.to_list(length=3)
    context = "
".join([doc["content"] for doc in retrieved_docs])

    chat_response = await ai_client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "Answer questions based on database context."},
            {"role": "user", "content": f"Context:
{context}

Question: {user_query}"}
        ]
    )

    await mongo_client.close()
    return chat_response.choices[0].message.content
```

### Vector Database Best Practices Checklist ✅
*   **Exclude Embeddings from Output:** Vectors are heavy (~12 KB per document). Exclude them using `{"$project": {"embedding": 0}}` to save network bandwidth.
*   **Tune numCandidates:** Balance recall accuracy and latency by setting it to 10× to 20× the limit.
*   **Workload Isolation:** Enable dedicated Atlas Search Nodes to isolate vector search compute from transactional workloads.
</details>
