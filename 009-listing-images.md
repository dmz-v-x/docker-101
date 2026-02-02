## Listing Docker Images on Your Machine

### 1. Why do we need to list Docker images?

Every time you:

- Pull an image from Docker Hub  
- Build an image using a Dockerfile  

Docker stores that image **locally** on your machine.

Over time, your system may contain:

- Multiple images
- Multiple versions of the same image
- Images you no longer use

Listing images helps you:

- See what images are available locally
- Identify unused images
- Debug build and pull issues
- Manage disk space

---

### 2. Command to list all Docker images

    docker images

This is one of the most commonly used Docker commands.

---

### 3. Breaking down the command

#### `docker`
Invokes the Docker CLI.

---

#### `images`
This command tells Docker:

> “Show me all Docker images stored locally on this machine”

No options are required for the basic usage.

---

### 4. What `docker images` actually does internally

When you run:

    docker images

Docker:

- Queries its local image store
- Retrieves metadata about each image
- Displays a formatted table in the terminal

It does **not** contact Docker Hub.  
This command works **offline**.

---

### 5. Understanding the output columns

Typical output looks like this:

    REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
    node            18        1b5c8c5a1e2f   2 weeks ago     942MB
    postgres        latest    7c3f2f9c1d9a   1 month ago     412MB

Let’s understand each column.

---

### 6. REPOSITORY

This is the **image name**.

Examples:
- `node`
- `postgres`
- `nginx`
- `online-store`

It usually matches the name used in:
- `docker pull`
- `docker run`

---

### 7. TAG

The **tag** represents the image version or variant.

Examples:
- `latest`
- `14`
- `18`
- `alpine`

If no tag is specified when pulling or running:
- Docker defaults to `latest`

---

### 8. IMAGE ID

This is a **unique identifier** for the image.

Key points:
- Shortened hash value
- Used internally by Docker
- Can be used instead of name + tag

Example usage:
    docker run 1b5c8c5a1e2f

---

### 9. CREATED

Shows **when the image was created**, not when you pulled it.

This helps you:
- Identify outdated images
- Understand image freshness

---

### 10. SIZE

Represents the **virtual size** of the image.

Important:
- Due to layering, this is not always real disk usage
- Shared layers are counted once on disk

Large images:
- Consume more disk
- Take longer to pull
- Slow down builds

---

### 11. Why image size matters

Large images lead to:

- Slower CI/CD pipelines
- Higher network usage
- Increased storage costs
- Slower container startup

This is why image optimization is important.

---

### 12. Difference between `docker images` and `docker image ls`

These two commands are equivalent:

    docker images
    docker image ls

The second is the **newer, structured form**.

---

### 13. Listing images with filters (basic idea)

You can filter images by name:

    docker images node

This shows:
- Only images with repository name `node`

---

### 14. Dangling images (important concept)

Dangling images are images that:

- Have no name
- Have no tag
- Are usually leftovers from builds

They appear as:

    <none>    <none>

These images:
- Take disk space
- Are safe to remove if unused

---

### 15. Why dangling images exist

They are created when:

- Images are rebuilt
- Tags are updated
- Old layers lose their reference

Docker keeps them to avoid accidental data loss.

---

### 16. Common beginner mistakes

- Assuming images are deleted automatically
- Confusing images with containers
- Deleting images still used by containers
- Ignoring image size growth

---

### 17. Local vs production image management

#### Local environment
- Many experimental images
- Frequent cleanup needed

#### Production environment
- Strict image versioning
- Limited image set
- Controlled lifecycle

---

### 18. Cleaning up unused images (conceptual)

While not deleting yet, know that:

- Unused images can be removed
- Disk space can be reclaimed
- Cleanup is part of good Docker hygiene

(Actual cleanup commands come later.)

---

### 19. Why `docker images` is a foundational command

This command helps you:

- Understand what exists locally
- Debug image-related issues
- Prepare for cleanup
- Manage Docker effectively

Almost every Docker workflow uses this command.



