## Listing Docker Containers 

### 1. What does “listing Docker containers” mean?

Listing Docker containers means **asking Docker to show containers that exist on your machine**.

Containers can be in different states:
- Running
- Stopped
- Exited
- Created but never started

Docker provides commands to list:
- Only running containers
- All containers (running + stopped)

This is a **core operational skill** in Docker.

---

### 2. Basic command to list running containers

	docker ps

This is the most commonly used Docker command.

---

### 3. What `docker ps` does internally

When you run:

	docker ps

Docker:
1. Talks to the Docker daemon
2. Queries active containers
3. Displays a formatted table
4. Shows only **running containers**

It does not:
- Start containers
- Stop containers
- Modify anything

It is a **read-only inspection command**.

---

### 4. Understanding the output of `docker ps`

Typical output:

	CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                    NAMES
	a1b2c3d4e5     nginx          "/docker-entrypoint…"   2 minutes ago   Up 2 minutes   0.0.0.0:80->80/tcp       web-server

Each row represents **one running container**.

---

### 5. Column-by-column explanation

---

### 5.1 CONTAINER ID

- Unique identifier for the container
- Shortened hash
- Used to manage the container

Example usage:

	docker stop a1b2c3d4e5

---

### 5.2 IMAGE

- Docker image used to create the container
- Shows name and sometimes tag

Example:
- nginx
- node:18
- postgres:15

This tells you **what software the container is running**.

---

### 5.3 COMMAND

- The command running inside the container
- Defined by CMD or ENTRYPOINT
- Truncated by default

This is the **main process** of the container.

---

### 5.4 CREATED

- When the container was created
- Not when it was started

Helpful for:
- Debugging old containers
- Understanding lifecycle timing

---

### 5.5 STATUS

Shows container state.

Common values:
- Up 5 minutes → running
- Exited (0) → stopped successfully
- Exited (1) → error
- Restarting → crash loop

STATUS is critical for debugging.

---

### 5.6 PORTS

Shows port mappings.

Example:

	0.0.0.0:3000->3000/tcp

Meaning:
- Host port 3000
- Forwarded to container port 3000

If empty:
- Container is not exposed externally

---

### 5.7 NAMES

- Human-readable container name
- Either user-defined or auto-generated

Example:
- my-node-app
- my-postgres

Using names is easier than IDs.

---

### 6. Listing all containers (running + stopped)

	docker ps -a

This command shows:
- Running containers
- Stopped containers
- Exited containers
- Containers created but never started

This is extremely important for cleanup and debugging.

---

### 7. Why `docker ps -a` matters

Containers do not disappear automatically.

Stopped containers:
- Still consume disk space
- Can block image deletion
- May cause confusion

Always check `docker ps -a` before cleanup.

---

### 8. Filtering container listings

You can filter containers by status.

Example: show only exited containers

	docker ps -a --filter status=exited

Example: show only running containers

	docker ps --filter status=running

Filters help in automation and cleanup scripts.

---

### 9. Listing containers by image

	docker ps --filter ancestor=nginx

This shows:
- Containers created from the nginx image

Useful when:
- Multiple containers use same image
- Debugging deployments

---

### 10. Displaying only container IDs

	docker ps -q

This outputs:
- Only container IDs
- One per line

Used heavily in scripts.

Example:

	docker stop $(docker ps -q)

---

### 11. Custom formatting output (advanced)

Docker allows custom output formatting.

Example:

	docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

This is useful for:
- CI/CD
- Monitoring scripts
- Custom dashboards

---

### 12. Difference between `docker ps` and `docker container ls`

These commands are equivalent:

	docker ps
	docker container ls

Newer Docker style prefers:

	docker container ls

Both work the same way.

---

### 13. Common beginner mistakes

- Thinking stopped containers are deleted
- Forgetting to use `-a`
- Confusing images with containers
- Ignoring STATUS column
- Removing images without checking containers

---

### 14. Local vs production usage

### Local environment
- Frequent use of `docker ps`
- Many stopped containers
- Manual cleanup common

### Production environment
- Containers monitored continuously
- STATUS used for health checks
- Automated cleanup policies

---

### 15. Why listing containers is a core Docker skill

Listing containers helps you:
- Understand what is running
- Debug application issues
- Manage system resources
- Prepare for cleanup
- Avoid accidental deletions

Every Docker workflow starts with:
> “What is currently running?”
