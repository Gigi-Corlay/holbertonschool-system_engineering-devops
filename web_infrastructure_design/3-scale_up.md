# 3. Scale Up Web Infrastructure

## Overview

This infrastructure is an improved version of a standard web stack designed to handle higher traffic, improve scalability, and enforce a strict separation of components.

It introduces dedicated servers for each layer of the system and a load balancer cluster to ensure high availability and fault tolerance.

The goal is to improve performance, maintainability, and reliability.

---

## Key Concept: Application Server vs Web Server

- The **web server (Nginx)** handles HTTP requests, serves static content, and acts as a reverse proxy.
- The **application server** executes business logic and generates dynamic content.

Separating these two components improves scalability and allows each layer to be optimized independently.

---

## Infrastructure Components

### 1. Load Balancer Cluster (HAProxy)

- Two HAProxy load balancers are configured in a cluster.
- They distribute incoming traffic across web servers.
- The cluster operates in **active-active or active-passive mode** depending on configuration.

**Why add it?**
To eliminate a single point of failure and ensure high availability of the system.

---

### 2. Web Server (Nginx) - Dedicated Server

- A dedicated server running Nginx.
- Handles incoming HTTP/HTTPS requests.
- Serves static content and forwards dynamic requests to the application server.

**Why add it?**
To isolate request handling and improve performance under high traffic.

---

### 3. Application Server - Dedicated Server

- A dedicated server responsible for executing application logic.
- Processes dynamic requests coming from the web server.
- Communicates with the database server.

**Why add it?**
To decouple business logic from HTTP handling and improve scalability.

---

### 4. Database Server (MySQL) - Dedicated Server

- A dedicated server used for data storage.
- Handles all read and write operations.

**Why add it?**
To isolate data management, improve performance, and enhance security.

---

## Architecture Design

- 1 dedicated server for the web layer (Nginx)
- 1 dedicated server for the application layer
- 1 dedicated server for the database layer
- 2 HAProxy load balancers configured as a cluster

Each component is isolated on its own server to improve scalability and fault isolation.

---

## Load Balancer Cluster Role

The HAProxy cluster provides:

- High availability (failover support if one load balancer fails)
- Load distribution across backend servers
- Improved system resilience

---

## Why This Architecture is Better

- Clear separation of concerns (web, application, database)
- Independent scaling of each layer
- Improved reliability and uptime
- Easier maintenance and deployment

---

## Limitations

- Increased infrastructure complexity
- Higher operational cost
- Requires synchronization between multiple servers
- More difficult debugging compared to a monolithic system
