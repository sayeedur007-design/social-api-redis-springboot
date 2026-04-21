# 🚀 Social API (Redis + Spring Boot)

A high-performance backend service designed to handle the velocity of modern social interactions. Built with **Spring Boot 3**, **PostgreSQL**, and **Redis**, this platform enforces strict concurrency guardrails and real-time viral scoring.

## 🛠️ Quick Start

Ensure you have **Docker** and **Docker Compose** installed. To launch the entire ecosystem (App, Database, and Cache) in a single command:

```bash
docker-compose up --build -d
```

Explore the live API at `http://localhost:8080/`.

---

## ⚡ Redis-Powered Architecture

We leverage Redis as a high-velocity state engine to handle interactions that are too frequent for traditional disk-based databases.

### 1. Atomic Interaction Guardrails
To protect the integrity of the platform, we implement strict, stateless limits at the Redis layer before any database write occurs:
- **Smart Bot Quota:** Every post is protected by a 100-reply cap for automated personas. We use atomic `INCR` operations to track interaction velocity without race conditions.
- **Interaction Lockdown:** Implements a 10-minute cooldown lock (`SET NX EX`) between specific bots and users to eliminate spam patterns.
- **Stateless Design:** No rollbacks or complex check-then-set flows; the Redis state is the immutable source of truth for interaction velocity.

### 2. Real-time Virality Scoring
Virality scores are calculated instantly on every Like (+20) and Comment (+50). By offloading these calculations from the main feed queries to an atomic Redis counter, we achieved ultra-low latency for "Hot Feed" retrieval.

### 3. Adaptive Notification Throttling
We respect user attention by enforcing a 15-minute delivery window for push notifications.
- **Throttling:** First interactions are delivered immediately. Subsequent alerts are queued.
- **Summary Sweeps:** Every 5 minutes, our background worker snapshots the pending list using an atomic `RENAME` operation to deliver a consolidated summary (e.g., *"Bot X and 5 others interacted with your posts"*).

---

## 📖 Interactive Documentation

The API includes built-in Swagger documentation for easy exploration:
- **Swagger UI:** [http://localhost:8080/swagger-ui/index.html](http://localhost:8080/swagger-ui/index.html)
- **API Dashboard:** [http://localhost:8080/](http://localhost:8080/)

## 🏗️ Reliable by Design

Our service ensures strict **atomicity** across all interaction points. By verifying velocity and cooldowns at the Redis layer *before* committing to PostgreSQL, we maintain a consistent and spam-free database even under high concurrency.

## ⚡ Key Features

- Atomic interaction control using Redis (no race conditions)
- Real-time virality scoring system
- Notification throttling with batching
- Stateless concurrency handling
- Reduced database load using in-memory operations