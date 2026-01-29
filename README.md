# Distributed Unique ID Generator Architecture

This repository contains the system architecture and design documentation for a distributed, high-throughput **Unique ID Generation service**. The project focuses on designing a **scalable, fault-tolerant, and low-latency** ID generation platform suitable for large-scale distributed systems.

The architecture is designed to support **millions of ID requests per second** while guaranteeing **global uniqueness**, collision prevention, and predictable performance under heavy load. It emphasizes **clear service boundaries**, horizontal scalability, and operational reliability rather than simple algorithmic ID generation.

---

## Project Scope

The system is designed as a **third-party ID generation service** consumed via **REST APIs**. Key features include:

- Support for standard ID formats: `UUID`, `ULID`, `Snowflake`
- Customizable ID formats per tenant
- Use cases: order tracking, authentication identifiers, analytics events, distributed transaction systems

This repository primarily documents:

- Architectural decisions
- Component responsibilities
- Data flow
- Scalability strategy
- Technology trade-offs


---

## High-Level Architecture

The system follows a **microservices architecture** optimized for **low latency** and **high throughput**.

**Key design points:**

- Client applications interact through an **API Gateway**, which is the single entry point.
- The gateway enforces **authentication**, **rate limiting**, and **request validation**.
- The **ID Generation Service** is **stateless** and horizontally scalable:
  - Generates collision-free IDs using **time-based** and **sequence-driven strategies**
  - Relies on **Redis** for atomic counters and cached configuration
  - Publishes ID events asynchronously to **Kafka** to avoid latency impact
- **Configuration data**, tenant metadata, and security credentials are persisted in **PostgreSQL** and cached in **Redis**
- Logging and audit trails are processed asynchronously via **Kafka consumers**
- **Monitoring and metrics** provide observability, alerting, and operational insight

---

## Core Components

- **API Gateway**  
  Entry point for all client traffic, handles authentication, rate limiting, and routing

- **Authentication Service**  
  Issues and validates JWTs and API keys for secure tenant and admin access

- **ID Generation Service**  
  Generates globally unique, collision-free IDs with sub-millisecond latency using stateless workers

- **Configuration Service**  
  Manages tenant-specific ID formats, namespaces, and generation rules

- **Caching Layer**  
  `Redis` used for atomic counters, rate limiting, and configuration caching

- **Database**  
  `PostgreSQL` stores structured system metadata and partitioned audit logs with strong consistency guarantees

- **Event Streaming**  
  `Kafka` used for high-throughput, durable, asynchronous logging and event processing

- **Monitoring and Metrics**  
  Tracks system health and performance

---

## Scalability and Reliability

- **Horizontal scaling** at every layer, stateless ID generation nodes allow linear scaling
- **Redis clusters**, **Kafka partitions**, and **PostgreSQL replicas** remove bottlenecks and isolate failures
- **Fault tolerance** via service isolation, asynchronous logging, and redundant infrastructure
- **Rate limiting and caching** ensure predictable performance during traffic spikes

---

## Data Design

- `PostgreSQL` provides **strong consistency**, relational integrity, and analytical querying
- High-volume logs partitioned by time to sustain millions of writes per second
- Strategic indexing allows fast dashboard queries without full table scans
- Control plane favors **strong consistency**, data plane favors **eventual consistency**

---

## Security

- Layered security controls:
  - API keys for tenant identification
  - JWTs for administrative access
  - TLS encryption for all communications
- **Role-based access control** restricts operations by client type
- Rate limiting mitigates abuse and denial-of-service scenarios

---

## Documentation

- Full system design, component breakdowns, trade-off analysis, and architecture diagrams are included in the uploaded **PDF**
- This README provides a **high-level summary**

---

## Intended Audience

This project is intended for:

- Backend engineers
- Platform engineers
- System designers interested in distributed systems, high-throughput services, and real-world architecture trade-offs
