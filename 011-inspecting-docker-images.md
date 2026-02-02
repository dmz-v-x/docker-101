## Inspecting Docker Images

### 1. What does “inspect a Docker image” mean?

Inspecting a Docker image means **looking inside the image metadata** to understand:

- How the image was built
- What layers it contains
- What configuration it has
- What default settings it uses when a container starts

This does **not** run the image.  
It only shows **detailed information** about it.

Think of it as:
> “Show me everything Docker knows about this image.”

---

### 2. Why image inspection is important

Inspecting images helps you:

- Debug image-related issues
- Understand what an image does by default
- Check exposed ports and environment variables
- Analyze layers and size
- Verify base image and architecture
- Prepare images for production use

This is a **must-know command** for real-world Docker usage.

---

### 3. Basic command to inspect an image

    docker inspect <image_name>:<tag>

Example:

    docker inspect node:18

---

### 4. Breaking down the command

- `docker` → Docker CLI
- `inspect` → Show detailed metadata
- `node:18` → Image name with tag

This command returns a **large JSON output**.

---

### 5. What `docker inspect` actually returns

Docker returns a JSON object containing:

- Image ID
- Repo tags
- Created timestamp
- Architecture and OS
- Image layers
- Environment variables
- Entrypoint and command
- Working directory
- Exposed ports
- Labels
- Storage driver info

This is raw, unfiltered data.

---

### 6. Understanding the most important fields (one by one)

Let’s break down the key sections you should focus on.

---

### 7. Image ID

Field:
    "Id"

This is:
- A unique hash
- Identifies the image internally
- Same value shown in `docker images`

Useful when:
- Removing images
- Comparing images
- Debugging duplicates

---

### 8. RepoTags

Field:
    "RepoTags"

Example:
    "RepoTags": ["node:18"]

This tells you:
- Image name
- Image tag
- Whether the image is tagged or dangling

If RepoTags is empty:
- Image is dangling (`<none>:<none>`)

---

### 9. Created

Field:
    "Created"

This shows:
- When the image was created
- Helpful for identifying outdated images

Important:
- This is **image build time**, not pull time

---

### 10. OS and Architecture

Fields:
    "Os"
    "Architecture"

Example:
- linux
- amd64

This matters when:
- Running on different hardware (ARM vs x86)
- Deploying to cloud servers
- Debugging “exec format error”

---

### 11. Image layers (RootFS)

Field:
    "RootFS"

Inside it:
    "Layers"

This shows:
- Each layer’s hash
- Order of layers
- How the image is stacked

This directly relates to **Docker image layering**.

---

### 12. Why layer inspection matters

Layer information helps you:

- Identify large layers
- Understand image size growth
- Optimize Dockerfiles
- Debug inefficient builds

Each layer corresponds to a Dockerfile instruction.

---

### 13. Config section (very important)

Field:
    "Config"

This defines how containers behave **by default**.

Inside `Config`, you’ll commonly see:

- `Env`
- `Cmd`
- `Entrypoint`
- `ExposedPorts`
- `WorkingDir`

---

### 14. Environment variables (`Env`)

Example:
    "Env": [
      "NODE_VERSION=18.19.0",
      "PATH=/usr/local/bin:..."
    ]

These variables:
- Are automatically available inside containers
- Can affect application behavior
- Can be overridden at runtime

---

### 15. CMD (default command)

Field:
    "Cmd"

This defines:
- What runs when the container starts
- If no command is provided in `docker run`

Example:
    "Cmd": ["node"]

If you override CMD:
- Docker uses your provided command instead

---

### 16. ENTRYPOINT (important distinction)

Field:
    "Entrypoint"

ENTRYPOINT:
- Defines the **main executable**
- Is harder to override than CMD

CMD provides default arguments  
ENTRYPOINT defines the executable itself

Understanding this is critical for advanced Docker usage.

---

### 17. Exposed ports

Field:
    "ExposedPorts"

Example:
    "3000/tcp"

Important clarification:
- EXPOSE does NOT publish ports
- It only documents which ports the app listens on

Actual access requires:
    -p host_port:container_port

---

### 18. Working directory

Field:
    "WorkingDir"

This is:
- Default directory inside the container
- Where commands are executed

If empty:
- Root (`/`) is used

---

### 19. Labels (metadata)

Field:
    "Labels"

Labels are:
- Key-value metadata
- Used for automation, monitoring, CI/CD
- Often added by image maintainers

Examples:
- Version info
- Maintainer info
- Build metadata

---

### 20. Inspecting image using IMAGE ID

You can also inspect by IMAGE ID:

    docker inspect 1b5c8c5a1e2f

This is useful when:
- Multiple tags point to same image
- Image name is ambiguous

---

### 21. Inspecting only specific fields (advanced usage)

You can filter output using Go templates.

Example:
    docker inspect -f '{{.Id}}' node:18

Example:
    docker inspect -f '{{.Config.Cmd}}' node:18

This is very useful for scripting and automation.

---

### 22. Difference between `docker inspect` and `docker history`

| Command | Purpose |
|------|--------|
| docker inspect | Full metadata & config |
| docker history | Layer creation & size |

Both are used together for deep image analysis.

---

### 23. Common beginner mistakes

- Being overwhelmed by JSON output
- Ignoring Config section
- Confusing EXPOSE with port publishing
- Not inspecting images before production use
- Assuming defaults without checking

---

### 24. Local vs production image inspection

#### Local environment
- Inspect images to learn
- Debug Dockerfiles
- Optimize image builds

#### Production environment
- Verify image configuration
- Validate ports and commands
- Confirm architecture compatibility
- Ensure security requirements

---

### 25. Why image inspection is a core Docker skill

Inspecting images helps you:

- Trust what you are running
- Avoid hidden defaults
- Debug startup issues
- Build better Dockerfiles
- Run safer containers

This command turns Docker from “magic” into something **transparent and understandable**.

