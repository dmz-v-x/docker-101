## Docker Networks — Introduction and Bridge Network

### 1. Why Networking Is Needed in Docker

When applications run on a normal machine, they communicate using:
- IP addresses
- Ports
- Network interfaces

Now think about containers:
- Each container is isolated
- Containers don’t automatically see each other
- Containers don’t automatically talk to the outside world

So the question becomes:

> How do containers talk to each other and to the outside world?

That problem is solved by **Docker networking**.

---

### 2. What Is a Docker Network?

A **Docker network** is a virtual network created and managed by Docker that allows:

- Containers to communicate with each other
- Containers to communicate with the host
- External users to access containerized applications

Docker networking abstracts:
- IP management
- Routing
- DNS
- Port forwarding

So you don’t manually configure networking like traditional servers.

---

### 3. What Docker Does Behind the Scenes

When Docker creates a network, it sets up:
- Virtual network interfaces
- IP address ranges
- Routing rules
- NAT (Network Address Translation)
- Firewall rules

All of this happens automatically.

That’s why Docker networking feels “simple” from the outside.

---

### 4. Types of Docker Networks (High-Level View)

Docker supports multiple network types, such as:
- Bridge
- Host
- None
- Overlay (used with Swarm / multi-host)

We start with **Bridge**, because:
- It is the default
- It is the most commonly used
- It teaches all core networking concepts

---

## BRIDGE NETWORK (CORE CONCEPT)

---

### 5. What Is the Bridge Network?

The **Bridge network** is the **default network type** in Docker.

If you do not explicitly specify a network:
- Docker automatically connects the container to the bridge network

In simple words:

> Bridge network is a private internal network created by Docker on the host machine.

---

### 6. Why It Is Called “Bridge”

Docker creates a **virtual network bridge** on the host.

This bridge:
- Acts like a virtual switch
- Connects containers together
- Separates them from the host’s main network

Containers are “plugged into” this bridge.

---

### 7. Isolation Provided by Bridge Network

Containers on the bridge network are:

- Isolated from the host’s main network
- Isolated from external networks
- Isolated from containers on other networks

They can only communicate:
- With containers on the same bridge network
- With the outside world if ports are explicitly mapped

This isolation is a **security feature**, not a limitation.

---

### 8. Private Internal Network

The bridge network creates:
- A private IP range (example: 172.x.x.x)
- An internal subnet
- Internal DNS resolution

Each container gets:
- Its own private IP address
- Within the bridge network

These IPs are **not directly accessible** from outside the host.

---

### 9. How Containers Communicate Inside Bridge Network

Containers on the same bridge network can:
- Talk to each other using IP addresses
- Talk to each other using container names (Docker DNS)

This enables:
- App ↔ Database communication
- Microservices communication
- Internal service discovery

---

### 10. External Access and Port Mapping

By default:
- Containers are NOT accessible from outside

To allow external access, Docker provides **port mapping**.

Port mapping means:
- A port on the host is mapped
- To a port inside the container

This is also called **port forwarding**.

---

### 11. Why Port Mapping Is Required

Without port mapping:
- The container app is running
- But no one outside can reach it

With port mapping:
- External users connect to host port
- Docker forwards traffic to container port

This keeps containers secure by default.

---

### 12. Bridge Network Use Case (Real-World Scenario)

Common setup:
- Web server container
- Database container

Requirements:
- Web server can talk to database
- Database should NOT be exposed publicly
- Web server should be accessible externally

Bridge network solves this perfectly.

---

### 13. Running a Web Server on Bridge Network

Command:

	docker run -d --name web-server --network bridge -p 8080:8080 my-web-app

Explanation step by step:
- docker run → create and start a container
- -d → run in detached mode
- --name web-server → assign container name
- --network bridge → connect to bridge network
- -p 8080:8080 → map host port to container port
- my-web-app → image name

Result:
- App runs inside container
- Accessible at http://localhost:8080
- Container is isolated internally

---

### 14. Running a MySQL Database on Bridge Network

Command:

	docker run -d --name mysql-db --network bridge -e MYSQL_ROOT_PASSWORD=my-secret-pw mysql

Explanation:
- mysql-db is connected to the same bridge network
- No ports are exposed
- Database is NOT accessible from outside
- Only containers on the same network can access it

This is exactly what we want for databases.

---

### 15. How Web Server Connects to MySQL

Inside the web-server container:
- Hostname → mysql-db
- Port → 3306 (default MySQL port)

Docker DNS resolves:
- mysql-db → container IP automatically

No hardcoded IPs needed.

---

### 16. Security Advantages of Bridge Network

Bridge network provides:
- Network isolation
- Controlled exposure
- Reduced attack surface
- Internal-only services

Only explicitly mapped ports are exposed.

---

### 17. Common Beginner Misunderstandings

- Thinking containers are public by default
- Forgetting port mapping
- Exposing databases unintentionally
- Using IPs instead of container names
- Assuming bridge = host network (it is not)

---

### 18. Default Bridge vs User-Defined Bridge (Preview)

Docker has:
- Default bridge network
- User-defined bridge networks

User-defined bridges:
- Have better DNS
- Better isolation
- Better control

We’ll cover that next.

---

### 19. Local vs Production Usage

### Local Development
- Bridge network used heavily
- Simple port mapping
- Multiple containers talking internally

### Production
- Bridge still used
- Often combined with:
  - Docker Compose
  - Custom networks
  - Firewalls
