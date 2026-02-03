## tmpfs Mounts in Docker

### 1. Why Another Storage Type Is Needed

So far, we’ve learned:
- Volumes → persistent storage
- Bind mounts → host-based storage
- Anonymous volumes → Docker-managed implicit storage

But sometimes you want:
- NO persistence
- NO disk writes
- MAXIMUM speed
- MAXIMUM security

This is where **tmpfs mounts** come in.

---

### 2. What Is a tmpfs Mount?

A **tmpfs mount** is a temporary filesystem that:

- Lives entirely in memory (RAM)
- Is never written to disk
- Exists only while the container is running
- Is destroyed when the container stops

In simple words:

> tmpfs mounts store data in RAM, not on disk.

Once the container stops → **all data is lost permanently**.

---

### 3. How tmpfs Mounts Work Internally

When Docker creates a tmpfs mount:
- It allocates memory from the host
- Mounts it into the container filesystem
- The container reads/writes as usual
- Data never touches the disk

From the application’s point of view:
> It looks like a normal directory  
> but it’s actually backed by memory

---

### 4. Persistence Behavior (Very Important)

tmpfs mounts are:

- NOT persistent
- NOT backed up
- NOT recoverable

When the container:
- Stops
- Crashes
- Is removed

👉 Data is gone forever.

This is intentional.

---

### 5. Why Are tmpfs Mounts Used?

tmpfs mounts exist for **very specific and important reasons**.

---

### 5.1 Temporary Storage

Use tmpfs when:
- Data is short-lived
- Data is intermediate
- Data is disposable

Examples:
- Temporary files
- Caches
- Runtime-generated artifacts
- Session data

---

### 5.2 Faster Performance

Because tmpfs uses RAM:
- No disk I/O
- No filesystem latency
- Extremely fast reads and writes

This is ideal for:
- High-performance workloads
- Temporary computation results

---

### 5.3 Security

Because data:
- Never touches disk
- Is wiped when container stops
- Cannot be recovered later

tmpfs is excellent for:
- Secrets
- Tokens
- Temporary credentials
- Sensitive in-memory data

This greatly reduces attack surface.

---

### 6. Basic Syntax for tmpfs Mounts

tmpfs mounts use the `--mount` flag.

General syntax:

	docker run --mount type=tmpfs,destination=<container-path> <image>

Key points:
- type=tmpfs → specifies in-memory filesystem
- destination → path inside the container

---

### 7. Example: Running a Container with tmpfs Mount

Command:

	docker run -d --name web-server --mount type=tmpfs,destination=/tmp tmpfsexample

---

### 8. Breaking Down the Example

- docker run  
  → Create and start container

- -d  
  → Run in detached mode

- --name web-server  
  → Container name

- --mount type=tmpfs,destination=/tmp  
  → Mount a tmpfs filesystem at /tmp inside container

- tmpfsexample  
  → Image name

---

### Result

- /tmp directory exists inside container
- All data written to /tmp is stored in RAM
- When container stops → /tmp data disappears

---

### 9. Common Use Case: /tmp Directory

Many applications:
- Write temporary files to /tmp
- Expect fast access
- Do not need persistence

Mounting /tmp as tmpfs:
- Improves performance
- Improves security
- Prevents disk clutter

---

### 10. tmpfs vs Other Storage Types (Comparison)

| Feature | Volume | Bind Mount | Anonymous Volume | tmpfs |
|------|-------|------------|------------------|-------|
| Stored on disk | Yes | Yes | Yes | No |
| Stored in memory | No | No | No | Yes |
| Persists after stop | Yes | Yes | Yes | No |
| Docker-managed | Yes | No | Yes | Yes |
| Fastest I/O | No | No | No | Yes |
| Secure for secrets | No | No | No | Yes |

---

### 11. Limitations of tmpfs Mounts

tmpfs mounts have important limitations:

- Data is lost on container stop
- Uses host RAM
- Excessive usage can cause memory pressure
- Not suitable for large data

They must be used carefully.

---

### 12. tmpfs and Memory Usage (Critical Concept)

tmpfs uses:
- Host memory
- Counts against system RAM
- Can impact other containers

If tmpfs grows too large:
- System may slow down
- Containers may be killed (OOM)

Always keep tmpfs usage minimal.

---

### 13. When You SHOULD Use tmpfs Mounts

Use tmpfs when:
- Data is temporary
- Data is sensitive
- Speed matters
- Persistence is unnecessary
- Security is a priority

---

### 14. When You Should NOT Use tmpfs Mounts

Avoid tmpfs when:
- Data must persist
- Data is large
- Data must be backed up
- You need disk-based storage
- RAM usage is constrained

Use volumes instead.

---

### 15. Local vs Production Usage

### Local Development
- Used occasionally
- Testing performance
- Temporary caching

### Production Environment
- Used for secrets
- Used for sensitive runtime data
- Used for high-performance temp storage
- Carefully monitored

---

### 16. Best Practices

- Use tmpfs only for truly temporary data
- Keep tmpfs directories small
- Never rely on tmpfs for persistence
- Monitor memory usage
- Combine with read-only containers for security

---

### 17. Mental Model to Remember

- Volume → disk + persistence
- Bind mount → host files
- Anonymous volume → Docker default storage
- tmpfs → memory only

This mental map avoids confusion.
