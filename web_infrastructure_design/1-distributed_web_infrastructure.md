# 1. Distributed Web Infrastructure

## Diagram

```text
                        [ USER / BROWSER ]
                                │
                                │ 1. Requests [www.foobar.com](https://www.foobar.com)
                                ▼
                         [ DNS SERVER ] ─── (Maps [www.foobar.com](https://www.foobar.com) to Load Balancer IP)
                                │
                                │ 2. Sends HTTP Requests
                                ▼
                     [ LOAD BALANCER (HAProxy) ]
                                │
         ┌──────────────────────┴──────────────────────┐
         │ 3. Distributes traffic (Round Robin)        │
         ▼                                             ▼
┌──────────────────────────────┐              ┌──────────────────────────────┐
│ WEB SERVER 1 (Nginx)         │              │ WEB SERVER 2 (Nginx)         │
│              │               │              │              │               │
│              ▼               │              │              ▼               │
│ APPLICATION SERVER           │              │ APPLICATION SERVER           │
│              │               │              │              │               │
│              ▼               │              │              ▼               │
│ APPLICATION FILES (Codebase) │              │ APPLICATION FILES (Codebase) │
└──────────────┬───────────────┘              └──────────────┬───────────────┘
               │                                             │
               │ 4. Writes data                              │ 5. Reads data
               ▼                                             ▼
┌──────────────────────────────┐              ┌──────────────────────────────┐
│ PRIMARY DATABASE (MySQL)     │ ───────────► │ REPLICA DATABASE (MySQL)     │
│ (Master - Read/Write)        │  Replication │ (Slave - Read Only)          │
└──────────────────────────────┘              └──────────────────────────────┘
```

# Specifics & Component Roles

## Why Additional Elements Were Added

* **Load Balancer (HAProxy):** Added to distribute incoming administrative network traffic across multiple web servers. This prevents any single web server from becoming a bottleneck and improves overall application availability.

* **Second Server Node:** Added to establish redundancy. If one server goes down or requires maintenance, the other server can continuously manage 100% of the traffic, avoiding immediate service downtime.


## Load Balancer Distribution Algorithm

The load balancer is configured with the **Round Robin** algorithm.

* **How it works:** It distributes client requests sequentially and evenly down the list of available backend servers. Request 1 goes to Server 1, Request 2 goes to Server 2, Request 3 loops back to Server 1, and so forth.

## Load Balancer Operational Setup: Active-Active vs. Active-Passive

This HAProxy setup is operating in an **Active-Active** configuration.

* **Active-Active:** The load balancer distributes connections across all backend servers simultaneously. Both servers are actively processing live traffic at the exact same time.

* **Active-Passive:** Only one server (the Active node) handles incoming traffic while the second server (the Passive/Standby node) remains idle. The Passive server only takes over if the Active server suffers a critical crash.


## Database Primary-Replica (Master-Slave) Cluster

* **How it works:** The primary database node acts as the central state mechanism. Any changes or data insertions are written to the Primary node, which records them into its binlog. The Replica node continuously reads this log and duplicates the data asynchronously to stay synchronized.

* **Application Differences:** The **Primary Node** handles all **Write** operations (inserting users, updating profiles, deleting posts) and can also handle reads.

    - The **Replica Node** is restricted to **Read** operations only (loading blog posts, viewing directories). The application isolates these tasks to offload heavy read traffic from the primary node.

## Infrastructure Issues & Limitations

* **SPOF (Single Points of Failure):** **The Load Balancer (HAProxy):** There is only one load balancer. If HAProxy crashes or its underlying server goes offline, no traffic can reach either of the functional web servers.

    - The Primary Database (MySQL): If the primary database crashes, the application can no longer execute write commands, breaking vital functionalities like user logins, registrations, or content creation.

* **Security Issues:**
    - **No Firewall:** There is no software or network firewall (like UFW or AWS Security Groups) restricting entry. Every port on every server is exposed to the open internet, increasing the vulnerability to malicious exploits.

    - **No HTTPS:** Traffic is transmitted via unencrypted HTTP. All data packets (including plaintext passwords and cookies) can be intercepted and read via man-in-the-middle attacks.

* **No Monitoring:**
    - There are no metric collection or alerting tools installed (such as Prometheus, Grafana, or Nagios). If a system resource peaks or a service fails secretly, the engineering team has no visibility into the infrastructure health until users complain.
    