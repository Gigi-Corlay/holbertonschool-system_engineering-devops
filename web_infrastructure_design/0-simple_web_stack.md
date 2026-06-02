# 0. Simple Web Stack Design

## Diagram

[ USER / BROWSER ] 
       │
       │ 1. Requests www.foobar.com
       ▼
[ DNS SERVER ] ─── (Translates www.foobar.com to IP 8.8.8.8)
       │
       │ 2. Sends HTTP/HTTPS Request (via TCP/IP)
       ▼
┌─────────────────────────────────────────────────────────────┐
│ SERVER (IP: 8.8.8.8)                                        │
│                                                             │
│   ┌─────────────────────────────────────────────────────┐   │
│   │ LAMP STACK                                          │   │
│   │                                                     │   │
│   │  [ WEB SERVER ] (Nginx)                             │   │
│   │        │                                            │   │
│   │        │ 3. Forwards dynamic requests               │   │
│   │        ▼                                            │   │
│   │  [ APPLICATION SERVER ]                             │   │
│   │        │                                            │   │
│   │        │ 4. Executes application logic              │   │
│   │        ▼                                            │   │
│   │  [ APPLICATION FILES ] (Codebase)                   │   │
│   │        │                                            │   │
│   │        │ 5. Queries or updates records              │   │
│   │        ▼                                            │   │
│   │  [ DATABASE ] (MySQL)                               │   │
│   └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘

## Specifics & Component Roles

* **What is a server?**
  A server is a computer program, a physical computer, or a virtual machine that waits for requests from other computers or programs (called "clients") and provides them with data, services, or functionality.

* **What is the role of the domain name?**
  The domain name (foobar.com) provides a human-readable, memorable name for users to access the website, map-matching it to a machine-readable numerical IP address so humans don't have to memorize numbers.

* **What type of DNS record 'www' is in www.foobar.com?**
  The 'www' record is typically configured as an **A record** that points directly to the server's IPv4 address (8.8.8.8), or as a **CNAME record** pointing to the root domain (foobar.com).

* **What is the role of the web server?**
  The web server (Nginx) is responsible for accepting incoming HTTP/HTTPS requests from clients, serving static content (like HTML, CSS, and images) directly, managing security certificates, and proxying dynamic requests to the application server.

* **What is the role of the application server?**
  The application server provides the environment to run the core logic of the program. It interprets the dynamic application files, processes business logic, and generates data to be handed back to the web server.

* **What is the role of the database?**
  The database (MySQL) provides structured storage for the web application. It securely organizes, updates, handles, and retrieves critical data like user accounts, comments, or product catalogs.

* **What is the server using to communicate with the computer of the user requesting the website?**
  The server communicates with the user's computer via data packets transmitted over the **TCP/IP** network protocol suite using application-layer protocols like **HTTP** or **HTTPS**.

## Infrastructure Issues & Limitations

* **SPOF (Single Point of Failure):**
  This architecture has a massive SPOF because everything (Nginx, Code, MySQL) runs on one single server. If the server hardware fails, the operating system crashes, or Nginx stops, the entire website goes completely offline instantly.

* **Downtime during maintenance:**
  Whenever updates need to be deployed, system patches applied, or software like Nginx or MySQL restarted, the website must be temporarily taken down. This causes immediate service interruption (downtime) for active users.

* **Cannot scale if there is too much incoming traffic:**
  This single server can only scale vertically (by upgrading its CPU or RAM). If a sudden traffic spike occurs, the server will quickly exhaust its resources (high CPU/RAM utilization), leading to slow response times, timed-out requests, or a complete server crash. It cannot distribute load among multiple machines.