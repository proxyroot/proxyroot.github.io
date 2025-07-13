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

```
                [Client Reads]
                    ↓
         ┌────────────────────┐
         │    Replica (Read)  │
         └────────────────────┘
                  ↑
[Writes] → ┌────────────────────┐
          │ Master (Write)     │
          └────────────────────┘
                  ↓
        ┌────────────────────┐
        │    Replica (Read)  │
        └────────────────────┘
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

```
        ┌─────────────┐
        │   Router    │
        └────┬────────┘
             ↓
   ┌─────────────┬─────────────┬─────────────┐
   │   Shard 1   │   Shard 2   │   Shard 3   │
   └─────────────┴─────────────┴─────────────┘
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

- Writes → Primary DB
- Reads → Read replicas

Improves read throughput and reduces load on primary.

---

## 🔍 Caching Layer

```
     [Client Request]
           ↓
    ┌──────────────┐
    │    Cache     │
    └──────┬───────┘
           ↓
       [Database]
```

- Tools: Redis, Memcached
- Handle cache invalidation carefully

---

## 📦 Polyglot Persistence

Use multiple databases for different data models:

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

```
           ┌─────────────┐
           │ Load Balancer│
           └──────┬───────┘
       ┌──────────┴──────────┐
   ┌───────┐           ┌───────┐
   │  US   │           │  EU   │
   │ DB    │           │ DB    │
   └───────┘           └───────┘
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
