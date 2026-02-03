## Tagging Docker Images

### 1. What does “tagging an image” mean in Docker?

In Docker, **tagging an image** means assigning a **human-readable name and version** to an image.

A Docker image tag follows this format:

    image_name:tag

Examples:
- `node:18`
- `postgres:15`
- `online-store:v1.0.0`

A tag is **just a label**, not a copy of the image.

---

### 2. Why tagging Docker images is important

Without proper tagging:

- You don’t know which version is deployed
- Rollbacks become difficult
- CI/CD pipelines become unreliable
- Production incidents become hard to debug

Tags solve these problems by providing:
- Versioning
- Clarity
- Traceability
- Consistency across environments

---

### 3. Default behavior if you don’t tag an image

If you build or pull an image **without specifying a tag**, Docker automatically uses:

    :latest

Example:
    docker pull node

Internally becomes:
    docker pull node:latest

Important clarification:
> `latest` does NOT mean “newest” or “stable”  
It is just a default tag name.

---

### 4. Basic command to tag a Docker image

    docker tag SOURCE_IMAGE TARGET_IMAGE

Example:
    docker tag online-store online-store:v1

---

### 5. Breaking down the `docker tag` command

- `docker` → Docker CLI
- `tag` → Assign a new tag
- `SOURCE_IMAGE` → Existing image
- `TARGET_IMAGE` → New name:tag

This command means:
> “Create another reference (tag) pointing to the same image”

No data is copied.

---

### 6. Tagging using image name and tag

Example:
    docker tag node:18 node:18.19.0

This results in:
- `node:18`
- `node:18.19.0`

Both tags point to the **same image ID**.

---

### 7. Tagging using IMAGE ID (very common)

You can also tag using the image ID.

Example:
    docker images

    docker tag 1b5c8c5a1e2f online-store:v2

This is useful when:
- Image has `<none>` tag
- Multiple tags exist
- You want precision

---

### 8. Verifying tags after tagging

After tagging, list images:

    docker images

You will see:
- Same IMAGE ID
- Multiple REPOSITORY:TAG entries

This confirms tagging was successful.

---

### 9. Important concept: Tags do NOT duplicate images

Very important rule:

> Docker tags are references, not copies

This means:
- Disk usage does NOT increase
- Removing one tag does NOT delete the image if other tags exist

The image is removed only when **no tags reference it**.

---

### 10. Tagging images for Docker Hub or registries

To push an image to Docker Hub or another registry, it must be tagged correctly.

Format:
    username/repository:tag

Example:
    docker tag online-store myusername/online-store:v1

This tells Docker:
> “This image belongs to my Docker Hub account”

---

### 11. Why registry tags are required

Docker registries organize images by:
- Namespace (username or org)
- Repository
- Tag

Without proper tagging:
- Push will fail
- Registry won’t accept the image

---

### 12. Tagging for different environments

A common real-world tagging strategy:

- `online-store:dev`
- `online-store:test`
- `online-store:staging`
- `online-store:prod`

This allows:
- Same image ID
- Different deployment stages
- Clear environment separation

---

### 13. Semantic versioning tags (best practice)

In production, tags often follow **semantic versioning**:

    MAJOR.MINOR.PATCH

Examples:
- `v1.0.0`
- `v1.1.0`
- `v2.0.0`

This enables:
- Controlled rollouts
- Easy rollbacks
- Clear change tracking

---

### 14. Retagging images (moving tags)

Tags can be moved.

Example:
    docker tag online-store:v1 online-store:latest

Now:
- `latest` points to `v1`
- Older `latest` reference is replaced

This is how production deployments are often updated.

---

### 15. Removing a tag vs removing an image

Removing a tag:
    docker rmi online-store:v1

If another tag exists:
- Image stays

If no tags remain:
- Image is deleted

This distinction is very important.

---

### 16. Common beginner mistakes

- Overusing `latest`
- Forgetting to tag before push
- Assuming tags copy images
- Deleting images accidentally by removing last tag
- Not versioning production images

---

### 17. Local vs production tagging strategy

#### Local development
- Flexible tags
- Experimental names
- Frequent rebuilds

#### Production
- Strict versioning
- Immutable tags
- No reuse of released tags

Once a version is released, it should never change.

---

### 18. How tagging fits into CI/CD

Typical pipeline flow:

1. Build image
2. Tag image with version
3. Scan image
4. Push image to registry
5. Deploy using tag

Tags become **deployment contracts**.

---

### 19. Why tagging is a core Docker skill

Tagging enables:
- Version control for containers
- Safe rollbacks
- Environment consistency
- Team collaboration
- Production reliability

Without tagging, Docker usage becomes chaotic.
