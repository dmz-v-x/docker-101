## Viewing Logs of a Docker Container

### 1. What are “container logs” in Docker?

Container logs are the **output produced by the main process running inside a container**.

Specifically, Docker captures:
- Standard Output (stdout)
- Standard Error (stderr)

In simple words:
> Whatever your application prints using `console.log`, `print`, `echo`, or logging frameworks  
> → Docker captures it as container logs

Docker does **not invent logs**.  
It only collects what the application already writes.

---

### 2. Why container logs are important

Container logs are used to:
- Debug application crashes
- Verify startup behavior
- Monitor runtime errors
- Investigate performance issues
- Confirm configuration values

In containers:
> Logs replace “SSH into server and check files”

---

### 3. Basic command to view container logs

	docker logs CONTAINER_NAME_OR_ID

Example:

	docker logs my-node-app

This shows **all logs produced so far** by the container.

---

### 4. Breaking down `docker logs`

- docker → Docker CLI
- logs → Fetch container logs
- CONTAINER_NAME_OR_ID → Target container

This command:
- Does NOT start or stop the container
- Works for running and stopped containers
- Is read-only

---

### 5. Viewing logs of a stopped container

Even if a container is stopped, logs are still available.

	docker logs my-container

This is very useful for:
- Debugging crashes
- Understanding why a container exited

---

### 6. Following logs in real time (very common)

	docker logs -f my-container

- `-f` means follow
- Shows logs as they are produced
- Similar to `tail -f`

Used heavily during:
- Development
- Live debugging

---

### 7. Viewing only the last N log lines

	docker logs --tail 50 my-container

This shows:
- Only the most recent 50 lines
- Avoids flooding the terminal

Very useful for large log outputs.

---

### 8. Combining follow + tail

	docker logs -f --tail 20 my-container

This means:
- Show last 20 lines
- Then continue streaming new logs

This is the **most commonly used log command**.

---

### 9. Viewing logs with timestamps

	docker logs -t my-container

- `-t` adds timestamps
- Helps correlate logs with events
- Important for debugging production issues

---

### 10. Filtering logs by time (advanced)

Show logs since a specific time:

	docker logs --since 10m my-container

Show logs until a specific time:

	docker logs --until 2026-02-02T10:30:00 my-container

This is useful when:
- Investigating incidents
- Narrowing down time windows

---

### 11. Viewing logs of multiple containers (pattern)

Docker does NOT natively support multi-container logs with one command.

Instead, you usually:
- Use Docker Compose
- Use log aggregation tools

But you can script:

	docker logs container1
	docker logs container2

---

### 12. Understanding what logs are NOT

Important clarification:

Docker logs:
- Are NOT application log files
- Are NOT rotated by default
- Are NOT structured unless your app outputs structured logs

Docker just captures stdout/stderr.

---

### 13. Where Docker stores logs internally (conceptual)

On Linux, Docker stores logs as:
- JSON files on disk
- One file per container

You usually should NOT access them directly.

Always prefer:
- docker logs
- Centralized logging tools

---

### 14. Log drivers (important concept)

Docker supports different **log drivers**.

Examples:
- json-file (default)
- syslog
- journald
- awslogs
- fluentd

Log driver controls:
- Where logs are stored
- How logs are shipped

By default:
- Logs stay local
- No automatic rotation

---

### 15. Common beginner mistakes

- Expecting logs when app prints nothing
- Forgetting to use `-f`
- Confusing logs with container stdout
- Writing logs to files inside container instead of stdout
- Not limiting log size

---

### 16. Best practice: Log to stdout/stderr

In containers, best practice is:

- Do NOT write logs to files
- Always log to stdout/stderr
- Let Docker or platform handle storage

This enables:
- docker logs
- Centralized logging
- Cloud-native observability

---

### 17. Logs vs exec (important difference)

- docker logs → what app printed
- docker exec → run commands inside container

Logs show **what already happened**  
Exec is for **interactive debugging**

---

### 18. Local vs production logging

### Local environment
- docker logs used directly
- Manual inspection
- Short-lived containers

### Production environment
- Logs shipped to centralized systems
- docker logs rarely used directly
- Structured logs preferred

---

### 19. Debugging a crashing container using logs

Typical flow:

1. Container exits unexpectedly
2. Check status:
	
	docker ps -a

3. View logs:
	
	docker logs my-container

4. Identify error
5. Fix issue
6. Rebuild or restart

Logs are your **first debugging tool**.
