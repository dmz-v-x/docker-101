## Working with Docker Networks

### 1. Why We “Work With” Docker Networks

By default, Docker gives you a bridge network automatically.
But in real applications, you often want:

- Isolation between applications
- Controlled communication
- Clean service discovery
- Predictable networking behavior

That’s why we **create and manage our own Docker networks**.

---

### 2. Step 1: Create a Docker Network

Before containers can communicate in a controlled way, we create a network.

### Command

	docker network create --driver bridge my-custom-network

---

### Breaking it down

- docker network create  
  → Tells Docker to create a new network

- --driver bridge  
  → Specifies the network type  
  → Bridge is the most commonly used driver for single-host setups

- my-custom-network  
  → Name of the network

---

### What Docker does internally

- Creates a virtual bridge on the host
- Assigns a private IP range
- Sets up internal DNS
- Prepares routing rules

Now Docker has a **private, isolated network** ready.

---

### 3. Step 2: Run a Container and Attach It to the Network

Now we start a container and explicitly attach it to our custom network.

### Command

	docker run -d --name web-app --network my-custom-network -p 8080:80 nginx

---

### Step-by-step explanation

- docker run  
  → Create and start a new container

- -d  
  → Run in detached (background) mode

- --name web-app  
  → Assign a readable container name

- --network my-custom-network  
  → Attach the container to our custom network

- -p 8080:80  
  → Map host port 8080 to container port 80  
  → This exposes the web app externally

- nginx  
  → Image name

---

### Result

- web-app container is running
- It has a private IP inside my-custom-network
- It is reachable externally on port 8080

---

### 4. Step 3: Run Another Container on the Same Network

Now we run a database container on the **same network**.

### Command

	docker run -d --name mysql-db --network my-custom-network -e MYSQL_ROOT_PASSWORD=mysecret-pw mysql

---

### Breaking it down

- --name mysql-db  
  → Container name (important for DNS resolution)

- --network my-custom-network  
  → Same network as web-app

- -e MYSQL_ROOT_PASSWORD=mysecret-pw  
  → Environment variable required by MySQL

- mysql  
  → Official MySQL image

---

### Important behavior

- mysql-db gets a private IP
- mysql-db is **NOT exposed to the outside world**
- Only containers in my-custom-network can access it

This is **secure by default**.

---

### 5. How Containers Communicate Inside the Network

Inside web-app container:

- Hostname: mysql-db
- Port: 3306 (MySQL default)

Docker’s internal DNS resolves:

	mysql-db → MySQL container IP

No hardcoded IPs.  
No manual networking.

---

### 6. Step 4: Connect a Running Container to a Network

Sometimes a container is already running, and you want to attach it to a network.

### Command

	docker network connect my-custom-network my-container

---

### What this does

- Attaches an existing container to the network
- Container gets a new IP on that network
- Container can now communicate with others on the network

Important:
- Container does NOT restart
- Connection happens dynamically

---

### 7. Step 5: List All Docker Networks

To see all networks on your system:

### Command

	docker network ls

---

### What you’ll see

- bridge
- host
- none
- my-custom-network

This helps you:
- Verify network creation
- Debug networking issues
- Clean up unused networks

---

### 8. Step 6: Inspect a Docker Network

To deeply understand a network’s configuration:

### Command

	docker network inspect my-custom-network

---

### What this shows

- Network driver
- Subnet and gateway
- Connected containers
- Container IP addresses
- Network options

This is your **network debugging tool**.

---

### 9. Step 7: Disconnect a Container from a Network

If a container should no longer communicate on a network:

### Command

	docker network disconnect my-custom-network <container_name>

Example:

	docker network disconnect my-custom-network mysql-db

---

### What happens

- Container loses network connectivity on that network
- Its IP on that network is removed
- Other containers can no longer reach it

This is useful for:
- Security isolation
- Maintenance
- Debugging

---

### 10. Complete Docker Networking Flow (Mental Model)

Here is the **correct order** you should always think in:

1. Create a network  
2. Run containers on that network  
3. Containers communicate via names  
4. Expose only required ports  
5. Inspect network when debugging  
6. Connect/disconnect containers as needed  

This is how real Docker networking works in practice.

---

### 11. Common Beginner Mistakes

- Letting Docker use default bridge unintentionally
- Forgetting to attach containers to the same network
- Exposing databases using -p
- Using IP addresses instead of container names
- Not inspecting networks during debugging

---

### 12. Local vs Production Usage

### Local Development
- User-defined bridge networks
- Simple port mapping
- Easy debugging

### Production
- Multiple networks per app
- Strict exposure rules
- Often combined with Docker Compose or Swarm

---

### 13. Best Practices

- Always use user-defined networks
- Never expose databases publicly
- Use container names for communication
- Inspect networks when things break
- Keep networking simple and intentional
