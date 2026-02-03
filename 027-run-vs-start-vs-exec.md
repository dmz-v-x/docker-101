## docker run vs docker start vs docker exec

### 1. Big Picture: Container Lifecycle (Mental Model)

Before comparing commands, you must understand **one core concept**:

Docker has **three different actions**:
1. Create a container
2. Start a container
3. Interact with a running container

Each command maps to **one specific responsibility**.

---

### 2. docker run — Create + Start a NEW Container

### 2.1 What `docker run` does

`docker run` is used to:

- Create a **new container**
- From an image
- And start it immediately

In simple words:

> docker run = create container + start container

Every time you use `docker run`, Docker creates a **brand new container**.

---

### 2.2 Basic syntax

	docker run [OPTIONS] IMAGE_NAME

---

### 2.3 Example

	docker run nginx

What happens internally:
1. Docker checks if `nginx` image exists locally
2. If not, it pulls the image
3. A new container is created
4. The container is started
5. The main process runs

---

### 2.4 Important characteristics of `docker run`

- Always creates a **new container**
- Old containers are NOT reused
- Container gets a new ID
- Container starts immediately
- Used for first-time runs

---

### 2.5 When to use `docker run`

Use `docker run` when:
- You are running an image for the first time
- You want a fresh container
- You want multiple containers from the same image
- You are experimenting or testing

---

### 3. docker start — Start an EXISTING (Stopped) Container

### 3.1 What `docker start` does

`docker start` is used to:

- Start a container that already exists
- The container must be stopped
- No new container is created

In simple words:

> docker start = start an existing container

---

### 3.2 Basic syntax

	docker start <container_name_or_id>

---

### 3.3 Example

	docker start my-nginx

What happens internally:
1. Docker finds the existing container
2. Restores its filesystem state
3. Re-attaches networking
4. Starts the original command

---

### 3.4 Important characteristics of `docker start`

- Does NOT create a new container
- Reuses the same container ID
- Preserves container data
- Uses the original CMD/ENTRYPOINT

---

### 3.5 When to use `docker start`

Use `docker start` when:
- A container was stopped earlier
- You want to resume the same container
- You want to keep container state
- You don’t want duplicates

---

### 4. docker exec — Run a Command INSIDE a Running Container

### 4.1 What `docker exec` does

`docker exec` is used to:

- Run a command inside a **running container**
- Without stopping or restarting it
- Without touching the main process

In simple words:

> docker exec = interact with a running container

---

### 4.2 Basic syntax

	docker exec [OPTIONS] <container_name_or_id> <command>

---

### 4.3 Example: Open a shell

	docker exec -it node-backend /bin/bash

What happens internally:
1. Docker creates a new process inside the container
2. The main application keeps running
3. You get an interactive shell

---

### 4.4 Example: Run a single command

	docker exec node-backend npm install express

This:
- Runs the command
- Shows output
- Exits
- Leaves container running

---

### 4.5 Important characteristics of `docker exec`

- Container must already be running
- Does NOT create or start containers
- Does NOT affect main process
- Used for debugging and inspection

---

### 4.6 When to use `docker exec`

Use `docker exec` when:
- You want to debug a running container
- You need a shell inside the container
- You want to inspect files or processes
- You want to run admin or debug commands

---

### 5. Core Differences (Side-by-Side Comparison)

| Feature | docker run | docker start | docker exec |
|------|-----------|-------------|------------|
| Creates container | Yes | No | No |
| Starts container | Yes | Yes | No |
| Requires existing container | No | Yes | Yes |
| Container must be running | No | No | Yes |
| Runs new process inside container | No | No | Yes |
| Used for interaction | No | No | Yes |
| Affects main process | Yes | Yes | No |

---

### 6. One-Line Mental Shortcut (Very Important)

Remember this forever:

- docker run → **New container**
- docker start → **Old container**
- docker exec → **Inside container**

If you remember this, you’ll never get confused.

---

### 7. Common Beginner Mistakes

- Using `docker run` instead of `docker start` (creates duplicates)
- Losing data by recreating containers
- Trying to exec into a stopped container
- Using exec to make permanent changes
- Confusing images with containers

---

### 8. Real-World Scenarios

### 8.1 First time running an app

	docker run -d -p 3000:3000 my-app

---

### 8.2 Restarting the same app container

	docker start my-app

---

### 8.3 Debugging a live container

	docker exec -it my-app /bin/bash

---

### 9. Best Practices

- Use `docker run` only for new containers
- Use `docker start` to reuse containers
- Prefer `docker exec` over `docker attach`
- Never rely on exec for permanent fixes
- Rebuild images for real changes

---
