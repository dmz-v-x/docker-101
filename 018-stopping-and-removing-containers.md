## Stopping and Removing Docker Containers

## PART 1: Stopping Docker Containers

---

### 1. What does “stopping a container” mean?

Stopping a Docker container means:

- Gracefully terminating the main process inside the container
- Allowing the application to shut down cleanly
- Keeping the container metadata and filesystem intact

Important:
- Stopping a container does NOT delete it
- The container still exists on disk

Think of it like:
> Stopping a laptop (not deleting it)

---

### 2. Why containers need to be stopped

Containers are stopped when:
- You want to restart the app
- You want to update the image
- You want to free system resources
- You want to remove the container

A container must be **stopped before removal** (in most cases).

---

### 3. Command to stop a running container

	docker stop CONTAINER_NAME_OR_ID

Example:

	docker stop my-node-app

---

### 4. Breaking down `docker stop`

- docker → Docker CLI
- stop → Gracefully stop a container
- CONTAINER_NAME_OR_ID → Target container

Docker sends:
- SIGTERM (terminate signal)
- Waits for shutdown
- Then SIGKILL if needed

---

### 5. Graceful shutdown behavior

By default:
- Docker waits 10 seconds
- Allows the app to clean up
- Then force-kills if still running

This is critical for:
- Databases
- Message queues
- File writes

---

### 6. Stopping multiple containers at once

	docker stop container1 container2 container3

Example:

	docker stop web db cache

---

### 7. Stopping all running containers

	docker stop $(docker ps -q)

Explanation:
- docker ps -q → list running container IDs
- docker stop → stops all of them

Useful during cleanup.

---

### 8. Force stopping a container

	docker kill CONTAINER_NAME_OR_ID

Difference:
- docker stop → graceful
- docker kill → immediate termination

Use `docker kill` only when:
- Container is unresponsive
- App ignores SIGTERM

---

## PART 2: Removing Docker Containers

---

### 9. What does “removing a container” mean?

Removing a container means:

- Deleting container metadata
- Deleting writable container layer
- Freeing disk space

Once removed:
- Container is gone permanently
- Cannot be restarted

This does NOT remove the image.

---

### 10. Command to remove a stopped container

	docker rm CONTAINER_NAME_OR_ID

Example:

	docker rm my-node-app

---

### 11. Breaking down `docker rm`

- docker → Docker CLI
- rm → remove
- CONTAINER_NAME_OR_ID → container to delete

Docker checks:
- Is container stopped?
- If yes → removes it
- If no → throws an error

---

### 12. Removing multiple containers

	docker rm container1 container2 container3

Example:

	docker rm web db cache

---

### 13. Removing all stopped containers

	docker container prune

What this does:
- Removes all stopped containers
- Does NOT touch running containers
- Asks for confirmation

Safe cleanup command.

---

### 14. Removing a running container (force remove)

	docker rm -f CONTAINER_NAME_OR_ID

What -f does:
- Stops the container
- Removes it immediately

Use carefully in production.

---

## 15. Removing containers by status (advanced)

Example: remove all exited containers

	docker rm $(docker ps -a -q -f status=exited)

This is useful in scripts.

---

### 16. Difference between stopping and removing

| Action | Stops Process | Deletes Container | Frees Disk |
|-----|--------------|------------------|-----------|
| docker stop | Yes | No | No |
| docker rm | No | Yes | Yes |
| docker rm -f | Yes | Yes | Yes |

---

### 17. Common beginner mistakes

- Trying to remove running containers without -f
- Assuming stopped containers are deleted
- Accidentally deleting important containers
- Using docker kill instead of stop
- Running prune in production without review

---

### 18. Local vs production behavior

### Local environment
- Frequent stopping/removing
- Manual cleanup
- Force removal is common

### Production environment
- Graceful shutdown preferred
- Containers managed by orchestrators
- Removal often automated
- Force kill is last resort

---

### 19. Best practices

- Always use docker stop before docker rm
- Avoid docker kill unless necessary
- Use names instead of IDs
- Review containers before pruning
- Never blindly force remove in production
