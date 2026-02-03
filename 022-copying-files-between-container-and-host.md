## docker cp Command

### 1. What is the `docker cp` command?

The `docker cp` command allows you to **copy files and directories between a Docker container and your local machine**.

In simple words:

> `docker cp` is like the `cp` (copy) command,  
> but it works **across the container boundary**.

It is mainly used to:
- Inspect files inside a container
- Debug configuration issues
- Extract generated files or logs
- Copy files without using volumes

---

### 2. When should you use `docker cp`?

You use `docker cp` when:

- A container is already running
- You want to inspect a file inside it
- You forgot to mount a volume
- You want a quick one-time copy
- You don’t want to rebuild the image

It is a **debugging and inspection tool**, not a deployment strategy.

---

### 3. Basic syntax of `docker cp`

	docker cp <source> <destination>

Where:
- One side must be a container
- The other side must be the local filesystem

---

### 4. Copying a file from container → local machine

### General syntax

	docker cp <container_id_or_name>:/path/to/file /local/path

---

### Example

	docker cp node-app-container:/usr/src/app/package.json ./package.json

---

### 5. Breaking down the example command

	docker cp node-app-container:/usr/src/app/package.json ./package.json

Explanation:
- `docker` → Docker CLI
- `cp` → copy command
- `node-app-container` → container name
- `:/usr/src/app/package.json` → file path inside container
- `./package.json` → destination on local machine

This means:
> “Copy `package.json` from inside the container to my current local directory”

---

### 6. Does the container need to be running?

Important rule:

- The container **does NOT need to be running**
- It can be:
  - Running
  - Stopped
  - Exited

As long as the container exists, `docker cp` works.

---

### 7. Copying a directory from container → local machine

	docker cp my-container:/usr/src/app ./app-backup

This copies:
- Entire `app` directory
- All files and subdirectories

---

### 8. Copying a file from local machine → container

You can also copy files **into** a container.

### Syntax

	docker cp /local/file/path <container_id_or_name>:/container/path

---

### Example

	docker cp ./config.json node-app-container:/usr/src/app/config.json

This is useful when:
- You want to test a config change
- You don’t want to rebuild the image

---

### 9. Overwriting behavior (important)

If:
- Destination file exists

Then:
- Docker **overwrites** it without warning

There is:
- No confirmation prompt
- No merge behavior

Be careful in production.

---

### 10. File permissions behavior

When copying files:

- Ownership may change
- Permissions may differ
- Depends on:
  - Container user
  - Host OS
  - Filesystem

This is expected behavior.

---

### 11. docker cp vs volumes (important distinction)

| docker cp | Docker volumes |
|---------|----------------|
| One-time copy | Continuous sync |
| Debugging tool | Production feature |
| Manual | Automatic |
| Not persistent | Persistent |

Never rely on `docker cp` for production data flow.

---

### 12. Common beginner mistakes

- Using docker cp instead of volumes
- Copying files into running production containers
- Forgetting container name or path
- Assuming cp keeps permissions identical
- Overwriting important files accidentally

---

### 13. Typical debugging workflow using docker cp

1. Container behaves unexpectedly
2. Copy config or file from container
	
	docker cp my-container:/path/file ./file

3. Inspect locally
4. Fix issue
5. Rebuild image or update configuration properly

---

### 14. Local vs production usage

### Local environment
- docker cp used often
- Debugging and inspection
- Quick experiments

### Production environment
- Rarely used
- Volumes or rebuilds preferred
- docker cp is emergency-only

---

### 15. Limitations of docker cp

- No live sync
- No partial copy
- No compression
- No exclude patterns

For complex workflows:
- Use volumes
- Use CI/CD
- Use artifact storage
