# Docker Image Layering

### 1. What is a Docker Image Layer?

In Docker, an **image layer** is a **read-only snapshot of the filesystem** created at a specific step while building a Docker image.

Every time Docker processes an instruction in a Dockerfile, it creates a new layer.

So a Docker image is not a single block — it is a **stack of layers** placed on top of each other.

---

### 2. Why Docker Uses Image Layers (The Core Idea)

Docker images are designed to be:

- Efficient
- Reusable
- Fast to build
- Easy to share

If Docker rebuilt everything from scratch every time, builds would be slow and images would be huge.

Image layers solve this by allowing Docker to:

- Cache work
- Reuse unchanged parts
- Share common layers across images

---

### 3. Mental Model for Image Layers

Think of image layers like:

- Transparent sheets stacked together
- Each sheet contains some files
- Final image = all sheets combined

Once a sheet is created:
- It never changes
- A new sheet is added on top for changes

---

### 4. Read-Only Nature of Image Layers

Important rule:

- **All image layers are read-only**

You cannot modify an existing layer.

If something changes:
- Docker creates a new layer
- The old layer remains untouched

This immutability is critical for caching and reuse.

---

### 5. How Docker Creates Layers (Dockerfile Perspective)

Consider this Dockerfile:

    FROM node:18
    WORKDIR /app
    COPY package.json .
    RUN npm install
    COPY . .
    CMD ["node", "index.js"]

Each instruction creates a new layer:

- FROM → base image layers
- WORKDIR → new layer
- COPY → new layer
- RUN → new layer
- COPY → new layer
- CMD → metadata layer

---

### 6. Which Dockerfile Instructions Create Layers?

Most instructions create layers:

- FROM
- RUN
- COPY
- ADD

Some instructions do not add filesystem layers but still affect the image:

- CMD
- ENTRYPOINT
- ENV
- EXPOSE

Understanding this helps optimize images.

---

### 7. Layer Ordering Matters (Very Important)

Docker builds images **top to bottom**.

If an instruction changes:
- All layers below it are rebuilt
- All layers above it are invalidated

This is why Dockerfile ordering is critical for performance.

---

### 8. Docker Build Cache (Core Optimization)

Docker caches layers during build.

If Docker sees:

- Same instruction
- Same input files
- Same context

It reuses the existing layer instead of rebuilding it.

This is called **layer caching**.

---

### 9. Reuse and Caching

This is the biggest advantage of layers.

Example:

- `npm install` layer only rebuilds if `package.json` changes
- Code changes do not invalidate dependency layers

This is why Dockerfiles often copy `package.json` first.

---

### 10. Efficient Storage and Sharing

Docker stores each layer only once on disk.

If:

- 10 images use the same base image
- Docker stores base layers once
- All images reference those layers

This saves:

- Disk space
- Network bandwidth

---

### 11. How Images Share Layers

Images can share:

- Base OS layers
- Runtime layers
- Dependency layers

For example:

- Many images use `node:18`
- They all share the same Node.js layers

---

### 12. Incremental Builds

Docker builds images incrementally:

- Only changed layers are rebuilt
- Unchanged layers are reused

This allows:

- Fast rebuilds
- Rapid iteration
- Efficient CI/CD pipelines

---

### 13. What Happens When a File Changes?

If you change a source file:

- Docker rebuilds the `COPY . .` layer
- All layers after it rebuild
- Layers before it are reused

This is why minimizing file changes in early layers matters.

---

### 14. Debugging and Transparency

Layers make Docker images easier to debug.

You can:

- Inspect image history
- See which instruction created which layer
- Understand image size growth

Command:

    docker history <image_name>

This shows:

- Layer sizes
- Commands used
- Build order

---

### 15. Inspecting Image Layers

You can inspect layers using:

    docker image inspect <image_name>

This provides:

- Layer IDs
- Metadata
- Configuration details

This transparency helps diagnose:

- Large images
- Inefficient Dockerfiles

---

### 16. Container Layers vs Image Layers

Important distinction:

- Image layers → read-only
- Container layer → writable

When a container runs:
- Docker adds a writable layer on top
- All file changes go there

This is called the **container layer**.

---

### 17. Copy-on-Write Mechanism

Docker uses **copy-on-write**.

When a container modifies a file:

- Original file remains in image layer
- Modified copy is written to container layer

This keeps images immutable and containers isolated.

---

### 18. Why Containers Are Lightweight

Containers are lightweight because:

- They reuse image layers
- Only store runtime changes
- No full OS duplication

Stopping or deleting a container removes only the container layer.

---

### 19. Image Size Optimization Using Layers

Bad layering:

    COPY . .
    RUN npm install

This rebuilds dependencies on every code change.

Good layering:

    COPY package.json .
    RUN npm install
    COPY . .

This maximizes cache reuse.

---

### 20. Multi-Stage Builds (Advanced Layering)

Multi-stage builds allow:

- Multiple images in one Dockerfile
- Only final layers included
- Smaller production images

Example:

    FROM node:18 AS build
    RUN npm run build

    FROM nginx
    COPY --from=build /app/dist /usr/share/nginx/html

Build tools are excluded from final image.

---

### 21. Layer Squashing (Advanced Concept)

Layer squashing:

- Combines multiple layers into one
- Reduces image size
- Loses layer history

Used carefully in production environments.

---

### 22. Common Layering Mistakes

- Too many RUN commands
- Copying everything too early
- Large base images
- Not using `.dockerignore`

These lead to large, slow images.

---

### 23. Best Practices for Layer Optimization

- Order instructions by change frequency
- Use small base images
- Leverage build cache
- Use multi-stage builds
- Clean up temp files in same RUN command

---

### 24. Final Summary

Docker image layering:

- Breaks images into reusable, immutable layers
- Enables caching and reuse
- Improves build speed
- Reduces storage usage
- Makes debugging easier

It is one of the **most important internal concepts of Docker**.
