---
title: "Scalable Database Architecture and Strategies"
date: 2024-08-19
categories: [system-design, databases]
tags: [scalability, availability, consistency, sharding, replication, CAP-theorem, database-architecture]
summary: "A robust, visual introduction to building scalable, resilient, and performant database systems. Covers CAP theorem, sharding, replication, caching, geo-distribution, and real-world case studies."
---

This guide explores the core principles, scaling strategies, and architectural designs for building scalable, resilient, and performant database systems.

---

## 🧠 Fundamentals

### CAP Theorem

The **CAP theorem** defines three core guarantees in distributed systems:

- **Consistency (C)**: Every read receives the most recent write.
- **Availability (A)**: Every request receives a response, regardless of node failure.
- **Partition Tolerance (P)**: The system continues operating despite network partitions.

> ✅ In practice, systems must prioritize two over the third, especially during network failures.

### Scaling Approaches

- **Vertical Scaling (Scale-Up)**: Increase hardware resources on a single node (e.g., more RAM, CPUs).
- **Horizontal Scaling (Scale-Out)**: Add more nodes and distribute the load across them.

---

## 🏗️ Architecture Patterns

### 1. Monolithic Database

All data lives in a single, centralized database.

- ✅ Simple to manage
- ❌ Limited scalability, high contention

---

### 2. Master-Slave (Leader-Follower) Replication

```mermaid
graph TD
    Client[Client Application] --> |Read Requests| Replica1[Replica 1<br/>Read-Only]
    Client --> |Write Requests| Master[Master Database<br/>Read/Write]
    Master --> |Replication| Replica1
    Master --> |Replication| Replica2[Replica 2<br/>Read-Only]
    Client --> |Read Requests| Replica2
    
    style Master fill:#ff9999
    style Replica1 fill:#99ccff
    style Replica2 fill:#99ccff
```

- Master handles all writes
- Replicas serve read requests
- Failover required for master failure

Use Cases:

- High read traffic
- Semi-real-time analytics

---

### 3. Multi-Master Replication

All nodes can perform writes. Conflict resolution is key.

```mermaid
graph TD
    Client1[Client 1] --> |Writes| Master1[Master 1<br/>Read/Write]
    Client2[Client 2] --> |Writes| Master2[Master 2<br/>Read/Write]
    Client3[Client 3] --> |Writes| Master3[Master 3<br/>Read/Write]
    
    Master1 <--> |Bidirectional<br/>Replication| Master2
    Master2 <--> |Bidirectional<br/>Replication| Master3
    Master1 <--> |Bidirectional<br/>Replication| Master3
    
    style Master1 fill:#ff9999
    style Master2 fill:#ff9999
    style Master3 fill:#ff9999
```

Pros:

- High availability
- Low latency for regional writes

Cons:

- Data conflicts
- Complex resolution logic

---

### 4. Sharding (Horizontal Partitioning)

Split data across nodes by a sharding key.

#### Techniques:

- **Range-Based Sharding**:
  - E.g., user IDs 1-1000 → shard A
- **Hash-Based Sharding**:
  - E.g., hash(user_id) % N → shard
- **Geo-Based Sharding**:
  - E.g., users in EU go to EU shard
- **Directory-Based Sharding**:
  - Use a lookup table to find data location

```mermaid
graph TD
    Client[Client Application] --> Router[Shard Router<br/>Load Balancer]
    Router --> |User ID 1-1000| Shard1[Shard 1<br/>Users 1-1000]
    Router --> |User ID 1001-2000| Shard2[Shard 2<br/>Users 1001-2000]
    Router --> |User ID 2001-3000| Shard3[Shard 3<br/>Users 2001-3000]
    
    style Router fill:#ffcc99
    style Shard1 fill:#99ff99
    style Shard2 fill:#99ff99
    style Shard3 fill:#99ff99
```

Challenges:

- Resharding requires migration
- Joins across shards are expensive

---

## ⚖️ Trade-Offs: Consistency vs Latency

### Strong Consistency

- Linearizable operations
- Useful for financial systems, inventory
- Usually requires synchronous replication or consensus protocols

### Eventual Consistency

- Updates propagate over time
- Suitable for social apps, caching layers

> 📌 Use CRDTs, vector clocks, or Lamport timestamps to resolve eventual consistency issues.

---

## 🔁 Read/Write Splitting

Separate writes from reads.

```mermaid
graph TD
    App[Application] --> |Write Operations| Primary[Primary Database<br/>Master]
    App --> |Read Operations| Replica1[Read Replica 1]
    App --> |Read Operations| Replica2[Read Replica 2]
    App --> |Read Operations| Replica3[Read Replica 3]
    
    Primary --> |Replication| Replica1
    Primary --> |Replication| Replica2
    Primary --> |Replication| Replica3
    
    style Primary fill:#ff9999
    style Replica1 fill:#99ccff
    style Replica2 fill:#99ccff
    style Replica3 fill:#99ccff
```

- Writes → Primary DB
- Reads → Read replicas

Improves read throughput and reduces load on primary.

---

## 🔍 Caching Layer

```mermaid
graph TD
    Client[Client Request] --> Cache[Cache Layer<br/>Redis/Memcached]
    Cache --> |Cache Hit| Response[Response]
    Cache --> |Cache Miss| DB[Database]
    DB --> Cache
    DB --> Response
    
    style Cache fill:#ffff99
    style DB fill:#ff9999
```

- Tools: Redis, Memcached
- Handle cache invalidation carefully

---

## 📦 Polyglot Persistence

Use multiple databases for different data models:

```mermaid
graph TD
    App[Application] --> |User Data| SQL[SQL Database<br/>PostgreSQL/MySQL<br/>Transactions]
    App --> |Document Data| NoSQL[NoSQL Database<br/>MongoDB/DynamoDB<br/>Flexible Schema]
    App --> |Metrics/Logs| TS[Time Series DB<br/>InfluxDB<br/>Time-based Queries]
    App --> |Search Queries| Search[Search Engine<br/>Elasticsearch<br/>Full-text Search]
    App --> |Cache| Cache[Cache Layer<br/>Redis<br/>Fast Access]
    
    style SQL fill:#99ccff
    style NoSQL fill:#99ff99
    style TS fill:#ffcc99
    style Search fill:#ff99cc
    style Cache fill:#ffff99
```

- SQL (PostgreSQL, MySQL) → Transactions
- NoSQL (MongoDB, DynamoDB) → Flexible schema
- Time Series (InfluxDB) → Metrics
- Search (Elasticsearch) → Full-text search

---

## 🌍 Geo-Distributed Databases

### Multi-Region Replication

- Copies data across geographic locations
- Reduces latency
- Improves fault tolerance

### Architecture Example

```mermaid
graph TD
    Users[Global Users] --> LB[Load Balancer<br/>Geo-Routing]
    LB --> |US Users| USDB[US Database<br/>Primary]
    LB --> |EU Users| EUDB[EU Database<br/>Replica]
    USDB <--> |Replication| EUDB
    
    style LB fill:#ffcc99
    style USDB fill:#99ccff
    style EUDB fill:#99ccff
```

---

## 🧪 Tools & Technologies

| Tool          | Description                                 |
| ------------- | ------------------------------------------- |
| PostgreSQL    | ACID, supports physical/logical replication |
| MongoDB       | Flexible schema, auto-sharding              |
| Cassandra     | AP system, tunable consistency              |
| Redis         | In-memory cache, pub/sub                    |
| Vitess        | Sharding layer for MySQL                    |
| YugabyteDB    | Geo-distributed PostgreSQL-compatible DB    |
| Amazon Aurora | Cloud-native SQL with replication           |
| CockroachDB   | CP system with strong consistency           |

---

## 📈 Scaling by Use Case

| Scenario           | Strategy                                   |
| ------------------ | ------------------------------------------ |
| High Read Volume   | Read replicas, caching, materialized views |
| High Write Volume  | Sharding, queueing, async ingestion        |
| Global Audience    | Geo-distributed clusters, CDNs             |
| Strong Consistency | Paxos/Raft, synchronous replication        |
| Low Latency        | Edge caching, region-aware routing         |

---

## 🧠 Best Practices

- Always benchmark and profile queries
- Use observability tools (e.g., Prometheus, Grafana)
- Automate failover and backups
- Prioritize idempotency and retry logic
- Plan for resharding from day one

---

## 🧪 Real-World Case Study: Instagram

- **Reads**: Offloaded to Memcached and read replicas
- **Writes**: Go to leader node
- **Media**: Stored in S3/CDN
- **Sharding**: By user ID to reduce contention
- **Consistency**: Eventual for feed, strong for messaging

---
