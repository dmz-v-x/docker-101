## Removing Docker Images 

### 1. Why do we need to remove Docker images?

Over time, your machine accumulates Docker images because:

- You pull many images for testing
- You build images repeatedly during development
- Old versions of images remain unused
- CI/CD builds leave intermediate images

If images are not removed:

- Disk space fills up
- Docker becomes slower
- Image management becomes confusing

Removing unused images is a **normal and necessary Docker practice**.

---

### 2. Important clarification: Image vs Container

Before removing images, you must understand this rule:

> **Docker will NOT allow you to remove an image that is being used by a container**

- Containers depend on images
- An image must be unused to be removed
- Running or stopped containers can block image deletion

So the order is always:

1. Stop containers (if needed)
2. Remove containers
3. Remove images

---

### 3. List images before removing them

Always check what images exist.

#### Command
    docker images

This shows:
- Repository
- Tag
- Image ID
- Size

You decide **which image should be removed**.

---

### 4. Removing a Docker image (basic command)

#### Command
    docker rmi <image_name>:<tag>

Example:
    docker rmi node:18

---

### 5. Breaking down `docker rmi`

- `docker` → Docker CLI
- `rmi` → remove image
- `node:18` → image name with tag

This command tells Docker:

> “Delete this image from my local machine”

---

### 6. Removing image using IMAGE ID

Sometimes images share the same name but different tags.

You can remove using **IMAGE ID**.

#### Command
    docker rmi <image_id>

Example:
    docker rmi 1b5c8c5a1e2f

IMAGE ID:
- Is unique
- Avoids ambiguity
- Very reliable

---

### 7. What happens internally when an image is removed

Docker performs these checks:

1. Is the image used by any container?
2. If yes → removal fails
3. If no → image layers are deleted
4. Shared layers are kept if used elsewhere

Docker never deletes shared layers blindly.

---

### 8. Error: image is being used by a container

Common error:

    Error response from daemon: conflict: unable to delete image

This means:
- A container is using the image
- Container may be running or stopped

---

### 9. Finding containers using an image

List all containers (including stopped ones):

    docker ps -a

Check:
- Which container uses the image
- Note container ID or name

---

### 10. Stop the container (if running)

    docker stop <container_name_or_id>

Example:
    docker stop my-node-app

---

### 11. Remove the container

    docker rm <container_name_or_id>

Example:
    docker rm my-node-app

Only after this can the image be removed.

---

### 12. Force removing an image (use carefully)

Docker allows forced removal.

#### Command
    docker rmi -f <image_name_or_id>

Example:
    docker rmi -f node:18

What `-f` does:
- Removes image even if used by stopped containers
- Automatically removes dependent stopped containers

⚠️ **Use with caution**, especially in production.

---

### 13. Removing multiple images at once

You can remove multiple images in one command.

#### Command
    docker rmi image1 image2 image3

Example:
    docker rmi node:14 postgres:13 nginx:latest

This is useful for bulk cleanup.

---

### 14. Dangling images (very important)

Dangling images are images that:

- Have `<none>` as name
- Have `<none>` as tag
- Are not referenced by any container

They are usually created during rebuilds.

---

### 15. Listing dangling images

    docker images -f dangling=true

These images:
- Take disk space
- Are safe to remove (usually)

---

### 16. Removing dangling images

    docker image prune

This command:
- Removes only dangling images
- Does NOT remove tagged images

It is safe and commonly used.

---

### 17. Removing all unused images (advanced cleanup)

#### Command
    docker image prune -a

What this does:
- Removes dangling images
- Removes unused tagged images
- Keeps images used by containers

This is aggressive cleanup.

---

### 18. Confirmation prompt (important)

Docker will ask:

    Are you sure you want to continue? [y/N]

This prevents accidental deletion.

---

### 19. Difference between prune and rmi

| Command | Purpose |
|------|--------|
| docker rmi | Remove specific images |
| docker image prune | Remove dangling images |
| docker image prune -a | Remove all unused images |

Use `rmi` for control  
Use `prune` for cleanup  

---

### 20. Local vs Production image removal

#### Local environment
- Frequent image removal
- Safe to prune aggressively
- Experimental images common

#### Production environment
- Image removal is rare
- Images tied to deployments
- Cleanup done carefully and intentionally

Never run aggressive prune commands blindly in production.

---

### 21. Common beginner mistakes

- Trying to remove images before containers
- Using `-f` without understanding impact
- Removing images still needed
- Confusing containers with images
- Running prune in production unintentionally

---

### 22. Disk space recovery (why removal matters)

Removing images:
- Frees disk space
- Speeds up Docker operations
- Reduces clutter
- Improves build performance

Good image hygiene is part of good Docker usage.



