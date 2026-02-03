## Restarting a Docker Container

### 1. What does “restarting a container” mean?

Restarting a container means:

- Stopping the running container
- Then starting the **same container again**
- Without creating a new container

Important:
- Container name stays the same
- Container ID stays the same
- Container filesystem (data inside container layer) is preserved

Think of it like:
> Turning an application off and back on using the same installation

---

### 2. Basic command to restart a container

	docker restart my-container

This is a **single command** that internally does:

- docker stop my-container
- docker start my-container

---

### 3. What happens internally during `docker restart`

When you run:

	docker restart my-container

Docker performs these steps in order:

1. Sends a graceful shutdown signal to the container
2. Waits for the container to exit
3. If the container stops in time → good
4. If not → Docker forcefully stops it
5. Starts the container again using the original command

No new container is created.

---

### 4. Graceful shutdown and the 10-second rule

By default, Docker:

- Sends a SIGTERM signal to the container
- Waits **10 seconds**
- If the container is still running → sends SIGKILL

This 10-second window is called the **grace period**.

Why this matters:
- Allows apps to close connections
- Allows databases to flush data
- Prevents corruption

---

### 5. Why Docker waits before forcing a stop

Applications need time to:

- Finish ongoing requests
- Save files
- Close database connections
- Release resources

A forced kill can cause:
- Data loss
- Corrupted state
- Partial writes

That’s why Docker uses a grace period.

---

### 6. Changing the grace period using `-t`

You can control how long Docker waits.

Example:

	docker restart -t 5 my-container

---

### 7. Breaking down `docker restart -t 5`

- docker → Docker CLI
- restart → restart container
- -t 5 → wait 5 seconds before force stop
- my-container → container name or ID

This means:
> “Give the container 5 seconds to shut down gracefully. If it doesn’t, kill it.”

---

### 8. When should you reduce the timeout?

Reducing the timeout makes sense when:

- Container shuts down quickly
- You want faster restarts
- You’re restarting stateless services
- You’re in development

Example:
- Web servers
- API services
- Workers

---

### 9. When should you increase the timeout?

Increasing the timeout is recommended when:

- Container runs a database
- Container handles long-running tasks
- Data consistency is critical

Examples:
- PostgreSQL
- MySQL
- Kafka
- Elasticsearch

---

### 10. Restarting multiple containers

You can restart more than one container at once.

	docker restart container1 container2 container3

Docker will restart them **one by one**.

---

### 11. Restarting all running containers

	docker restart $(docker ps -q)

This:
- Finds all running containers
- Restarts each of them

Useful during:
- Local development resets
- Configuration reloads

Be careful in production.

---

### 12. Difference between restart, stop/start, and kill

| Command | Graceful | Force Kill | Creates New Container |
|------|--------|-----------|---------------------|
| docker stop | Yes | After timeout | No |
| docker start | No | No | No |
| docker restart | Yes | After timeout | No |
| docker kill | No | Immediate | No |

---

### 13. Restart vs Restart Policy (important distinction)

`docker restart`:
- Manual action
- Triggered by user

Restart policy:
- Automatic
- Triggered by Docker daemon
- Used for crash recovery

They solve **different problems**.

---

### 14. Common beginner mistakes

- Restarting containers instead of rebuilding images
- Using restart for configuration changes that need rebuild
- Setting timeout too low for databases
- Restarting production containers blindly

---

### 15. Local vs production behavior

### Local environment
- Restart often
- Short timeouts acceptable
- Used for quick iteration

### Production environment
- Restart carefully
- Grace periods matter
- Often handled by orchestrators
- Manual restarts are rare

---

### 16. Best practices

- Use default timeout unless you know better
- Increase timeout for stateful services
- Restart only when needed
- Monitor logs after restart
- Prefer graceful shutdowns

