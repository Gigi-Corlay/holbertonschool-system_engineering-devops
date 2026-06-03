# 3. Scale Up Web Infrastructure

## Overview

This infrastructure is an improved and scaled version of a standard web stack. It is designed to handle higher traffic, improve system reliability, and ensure better separation of responsibilities between components.

The main improvements include splitting each layer of the system onto dedicated servers and introducing a load balancer cluster to ensure high availability and fault tolerance.

---

## Application Server vs Web Server

### Web Server (Nginx)

The web server is responsible for:
- Handling incoming HTTP/HTTPS requests from users
- Serving static content (HTML, CSS, images, JavaScript)
- Acting as a reverse proxy for dynamic requests

### Application Server

The application server is responsible for:
- Executing the business logic of the application
- Processing dynamic requests
- Communicating with the database server to retrieve or store data

### Key Difference

The web server handles client requests and routing, while the application server processes the actual application logic.

---

## Infrastructure Components

### 1. Load Balancer Cluster (HAProxy)

- Two HAProxy load balancers are deployed in a cluster configuration.
- They distribute incoming traffic across backend servers.

**Why add it?**
To ensure high availability and prevent a single point of failure. If one load balancer fails, the second one continues handling traffic.

---

### 2. Web Server (Nginx)

- Dedicated server running Nginx.
- Handles HTTP/HTTPS requests and forwards dynamic requests to the application server.

**Why add it?**
To isolate HTTP request handling and improve performance and scalability under heavy traffic.

---

### 3. Application Server

- Dedicated server responsible for running the application logic.
- Processes dynamic requests and interacts with the database server.

**Why add it?**
To separate business logic from web serving, improving scalability, maintainability, and performance.

---

### 4. Database Server (MySQL)

- Dedicated server for data storage and management.
- Handles all read and write operations.

**Why add it?**
To isolate data storage, improve performance, enhance security, and allow independent scaling of the database layer.

---

## Architecture Summary

This infrastructure is composed of:

- 1 server for the web layer (Nginx)
- 1 server for the application layer
- 1 server for the database layer
- 2 HAProxy load balancers configured as a cluster

Each component is deployed on a dedicated server to ensure proper separation of concerns.

---

## Benefits of This Architecture

- **High Availability:** Load balancer cluster prevents single point of failure
- **Scalability:** Each layer can be scaled independently
- **Maintainability:** Clear separation between components
- **Reliability:** Reduced risk of system-wide failure
- **Performance:** Dedicated resources for each service

---

## Limitations

- Increased infrastructure complexity
- Higher cost due to multiple servers
- Requires proper synchronization between components
- More difficult to manage than a monolithic architecture
