## Docker Image Optimization and Security Inspection

## PART 1: Image Optimization Using `docker inspect` + `docker history`

### 1. Why Docker image optimization matters

Docker images are shipped across:

- Developer machines
- CI/CD pipelines
- Staging environments
- Production servers

If images are not optimized, you face:

- Large image sizes
- Slow builds
- Slow pulls
- Higher storage and network costs
- Slower deployments

Optimization is **not optional** in real-world Docker usage.

---

### 2. What does “image optimization” actually mean?

Image optimization means:

- Reducing image size
- Reducing number of layers
- Improving build cache usage
- Removing unnecessary files
- Making images predictable and efficient

To do this properly, we must **inspect what’s inside the image**.

---

### 3. Two key tools for optimization

Docker gives us two powerful inspection tools:

- `docker history` → How the image was built
- `docker inspect` → How the image behaves

Used together, they show:
- Where size comes from
- Which layers are heavy
- Which instructions caused inefficiency

---

### 4. Understanding `docker history`

#### Command
    docker history <image_name>:<tag>

Example:
    docker history node:18

---

### 5. What `docker history` shows

The output includes:

- Layer size
- Dockerfile instruction
- Creation order
- Layer ID

This directly maps to **Dockerfile instructions**.

Each line corresponds to **one layer**.

---

### 6. Why `docker history` is critical for optimization

It helps you answer:

- Which layer is largest?
- Which instruction added most size?
- Are there unnecessary layers?
- Are files being added then removed later?

If a layer is large, it stays large forever.

---

### 7. Common optimization issue found via history

Example problem:

    RUN apt-get update
    RUN apt-get install -y curl
    RUN rm -rf /var/lib/apt/lists/*

This creates **three layers**, even though cleanup happened.

Correct approach:

    RUN apt-get update \
        && apt-get install -y curl \
        && rm -rf /var/lib/apt/lists/*

Now:
- One layer
- Smaller final image

---

### 8. Understanding `docker inspect` for optimization

#### Command
    docker inspect <image_name>:<tag>

This command shows **configuration and metadata**, not layer sizes.

---

### 9. Optimization insights from `docker inspect`

From `docker inspect`, you can identify:

- Base image used
- Environment variables
- Default command
- Exposed ports
- Working directory
- Architecture

This helps detect:

- Overly large base images
- Unnecessary defaults
- Misconfigured images

---

### 10. Checking base image choice (very important)

From `inspect`, look at:

- OS
- Architecture
- Parent image

If you see:
- `ubuntu`
- `debian`

Ask:
- Can this be replaced with `alpine` or `distroless`?

Base image choice often determines **70–80% of image size**.

---

### 11. Using `.dockerignore` (optimization must-know)

Optimization is not only about layers.

If you don’t use `.dockerignore`:
- Entire project directory is sent to Docker
- Builds become slow
- Images become bloated

Always ignore:
- `node_modules`
- `.git`
- Logs
- Build artifacts

---

### 12. Multi-stage builds (major optimization technique)

Multi-stage builds allow:

- Separate build environment
- Minimal runtime environment
- Smaller final images

Example concept:

- Stage 1: build app
- Stage 2: run app

Build tools never reach production image.

---

### 13. Optimization checklist (summary)

Using `inspect` + `history`, check:

- Large layers
- Layer ordering
- Base image size
- Redundant RUN instructions
- Missing `.dockerignore`
- Build tools leaking into final image

---

## PART 2: Security Inspection of Docker Images

### 14. Why image security matters

Docker images can contain:

- Vulnerable OS packages
- Outdated libraries
- Hardcoded secrets
- Malicious binaries

If an image is compromised:
- Every container using it is compromised

Image security is a **supply-chain concern**.

---

### 15. What “security inspection” means in Docker

Security inspection means checking:

- What software is inside the image
- How the image runs
- What privileges it has
- Whether it exposes attack surfaces

---

### 16. Using `docker inspect` for security checks

From `docker inspect`, focus on:

- `User`
- `Env`
- `Cmd`
- `Entrypoint`
- `ExposedPorts`
- `Volumes`

---

### 17. Running as root (critical security issue)

Check field:
    "User"

If empty:
- Container runs as **root by default**

Running as root means:
- High privilege inside container
- Higher impact if compromised

Best practice:
- Use a non-root user in Dockerfile

---

### 18. Environment variables and secrets

Inspect:
    "Env"

Look for:
- Passwords
- Tokens
- API keys
- Database credentials

Secrets should:
- Never be baked into images
- Be injected at runtime

Hardcoded secrets = security vulnerability.

---

### 19. Exposed ports (attack surface)

Inspect:
    "ExposedPorts"

Ask:
- Are all exposed ports required?
- Is database port exposed unintentionally?

More exposed ports = larger attack surface.

---

### 20. ENTRYPOINT and CMD security risks

Inspect:
- `Entrypoint`
- `Cmd`

Check:
- Are unknown scripts executed?
- Are shell commands used unnecessarily?
- Is `sh -c` used incorrectly?

Misconfigured startup commands can allow command injection.

---

### 21. Image source verification

Security rule:

> Never trust random images blindly

Always check:
- Official image badge
- Maintainer
- Documentation
- Image popularity

Prefer:
- Official images
- Verified publishers

---

### 22. Image vulnerabilities (conceptual overview)

Images may contain:
- OS vulnerabilities
- Library CVEs
- Runtime vulnerabilities

These are not visible via inspect alone.

In real systems, teams use:
- Vulnerability scanners
- CI security checks

But inspect helps verify **runtime safety**.

---

### 23. Reducing image attack surface (security + optimization)

Security and optimization overlap.

Smaller images:
- Have fewer packages
- Have fewer vulnerabilities
- Are easier to audit

This is why:
- Alpine
- Distroless
are popular in production.

---

### 24. Security checklist using `inspect`

Before running an image in production, verify:

- Runs as non-root
- No secrets in ENV
- Only required ports exposed
- Minimal base image
- Trusted image source
- Clear CMD and ENTRYPOINT

---

### 25. Local vs production security inspection

#### Local environment
- Learn image internals
- Experiment freely
- Debug behavior

#### Production environment
- Mandatory inspection
- Version pinning
- Security scanning
- Approval workflows

Production images must be **audited artifacts**, not experiments.
