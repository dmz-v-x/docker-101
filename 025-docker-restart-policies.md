## Docker Restart Policies

### 1. What are Docker restart policies?

Docker restart policies define **what Docker should do with a container when it stops**.

In simple terms:

> Restart policies tell Docker  
> “If this container stops, should I restart it automatically?”

This is extremely important for:
- Reliability
- Crash recovery
- Long-running services
- Production stability

Without restart policies, **a crashed container stays down forever**.

---

### 2. Why restart policies are needed

Containers can stop for many reasons:

- Application crashes
- Unhandled exceptions
- Out-of-memory errors
- Server reboots
- Docker daemon restarts

Restart policies allow Docker to:
- Automatically recover services
- Reduce manual intervention
- Keep applications running

---

### 3. Where restart policies are defined

Restart policies are defined:

- At container creation time
- Using the `--restart` flag
- With `docker run`
- Or in Docker Compose files

They are **container-level behavior**, not image-level.

---

### 4. Basic syntax of restart policy

	docker run --restart=<policy> IMAGE_NAME

Example:

	docker run --restart=always nginx

---

### 5. Important rule about restart policies

Restart policies:

- Are enforced by the Docker daemon
- Work only when Docker daemon is running
- Do NOT restart containers if Docker itself is stopped

Docker must be alive for restart policies to work.

---

### 6. Types of Docker restart policies

Docker supports **four restart policies**:

1. no (default)
2. always
3. unless-stopped
4. on-failure

We’ll go through each one in detail.

---

### 7. Restart policy: `no` (default)

### Definition

	no

This is the default behavior.

### Meaning

- Docker will NOT restart the container
- If the container stops, it stays stopped

### When it is used

- One-off containers
- Batch jobs
- Development experiments
- Short-lived tasks

Example:

	docker run --restart=no ubuntu

---

### 8. Restart policy: `always`

### Definition

	always

### Meaning

- Docker will always restart the container
- If it crashes → restart
- If Docker daemon restarts → restart
- Even if container exits normally → restart

### Example

	docker run --restart=always nginx

---

### Key behavior of `always`

- Container restarts indefinitely
- Docker daemon reboot triggers restart
- Manual stop behaves differently (important!)

If you manually stop the container:

	docker stop my-container

Docker will:
- Keep it stopped
- Restart it automatically when Docker daemon restarts

---

### When to use `always`

- Web servers
- APIs
- Background workers
- Long-running services

---

### 9. Restart policy: `unless-stopped`

### Definition

	unless-stopped

### Meaning

- Same as `always`
- EXCEPT when container is manually stopped

### Example

	docker run --restart=unless-stopped nginx

---

### Key difference from `always`

If you manually stop the container:

	docker stop my-container

Docker will:
- NOT restart it
- Even after Docker daemon restart

This makes `unless-stopped` more human-friendly.

---

### When to use `unless-stopped`

- Most production services
- Services you may want to stop intentionally
- Long-running applications

This is the **most commonly recommended policy**.

---

### 10. Restart policy: `on-failure`

### Definition

	on-failure

### Meaning

- Docker restarts the container
- ONLY if it exits with a non-zero exit code
- Normal exits are NOT restarted

---

### Example

	docker run --restart=on-failure my-job

---

### Exit codes matter here

- Exit code 0 → success → no restart
- Exit code ≠ 0 → failure → restart

This is ideal for:
- Batch jobs
- Workers
- Retry-based tasks

---

### Limiting restart attempts

You can limit retries:

	docker run --restart=on-failure:5 my-job

Meaning:
- Restart at most 5 times
- Then stop permanently

---

### 11. Restart policies and exit codes (important concept)

Docker decides restart behavior based on:

- Exit code of main process
- Restart policy configuration

So application developers must:
- Use correct exit codes
- Signal failure properly

Exit codes become part of system design.

---

### 12. Checking restart policy of a container

To inspect restart policy:

	docker inspect my-container

Look for:

	"RestartPolicy": {
	  "Name": "always",
	  "MaximumRetryCount": 0
	}

---

### 13. Changing restart policy of an existing container

You can update restart policy without recreating container:

	docker update --restart=unless-stopped my-container

This is very useful in production.

---

### 14. Restart policy vs docker restart command

Important distinction:

| Concept | Purpose |
|------|--------|
| docker restart | Manual restart |
| Restart policy | Automatic restart |

Restart policies do NOT trigger immediate restarts.  
They define behavior **when a container stops**.

---

### 15. Restart policies vs orchestration tools

Restart policies are:
- Basic reliability mechanism
- Good for single-host Docker

In large systems:
- Kubernetes
- Docker Swarm
handle restarts more intelligently.

Restart policies are still foundational knowledge.

---

### 16. Common beginner mistakes

- Assuming restart policy restarts Docker daemon
- Using `always` everywhere blindly
- Not understanding manual stop behavior
- Using restart policies for batch jobs incorrectly
- Forgetting exit codes matter

---

### 17. Local vs production usage

### Local development
- Often no restart policy
- Manual control preferred
- Debugging-focused

### Production environment
- Restart policies mandatory
- `unless-stopped` preferred
- Combined with monitoring

---

### 18. Best practices

- Use `unless-stopped` for services
- Use `on-failure` for jobs/workers
- Avoid `always` unless required
- Set retry limits for failure-based restarts
- Monitor containers even with restart policies

Restart policies help recovery, not root cause fixing.

---

### 19. Real-world examples

### Web server

	docker run -d --restart=unless-stopped -p 80:80 nginx

---

### Background worker

	docker run -d --restart=on-failure:3 worker-app

---

### One-time script

	docker run --restart=no migration-job

