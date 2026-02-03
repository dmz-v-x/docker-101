### Docker Best Practices

### 1. Use Official Docker Images as Base Images

Always prefer **official images** published and maintained by Docker or trusted organizations.

Why this matters:
- Official images are well-maintained
- Regular security patches
- Clear documentation
- Predictable behavior

Examples:
- node
- python
- nginx
- mysql
- postgres

Avoid:
- Random images with unknown maintainers
- Images without documentation
- Images with unclear update policies

Mental model:
Official image = trusted foundation

---

### 2. Use Specific Image Versions (Avoid `latest`)

Never rely on the `latest` tag.

Bad:
- FROM node:latest

Good:
- FROM node:18
- FROM node:18-alpine
- FROM node:18-slim

Why `latest` is dangerous:
- Image contents can change anytime
- Builds become non-reproducible
- A rebuild tomorrow may break even if code didn’t change

Best practice:
Pin exact versions so builds are deterministic.

---

### 3. Prefer Smaller Base Images (Alpine / Slim / Distroless)

Smaller images provide:
- Faster downloads
- Faster deployments
- Smaller attack surface
- Less disk usage

Common choices:
- alpine → very small, but musl libc quirks
- slim → safer compatibility, still small
- distroless → best for production runtime

Rule of thumb:
- Development → slim
- Production → distroless (or alpine if suitable)

Smaller image ≠ always better  
Correct image for the stage is better.

---

### 4. Optimize Docker Image Layer Caching

Docker builds images **layer by layer**.  
If a layer doesn’t change, Docker reuses it.

Key principle:
Order Dockerfile instructions from  
least-changing → most-changing

Correct pattern:
- Base image
- Install system dependencies
- Install app dependencies
- Copy application code

Example logic (Node.js):
- Copy package.json
- Run npm install
- Copy source code

Why this works:
- Dependencies change less often than code
- Docker reuses cached dependency layers
- Builds become much faster

Poor ordering leads to slow builds and wasted resources.

---

### 5. Use `.dockerignore` to Exclude Unnecessary Files

`.dockerignore` prevents files from being sent to the Docker build context.

Why this is critical:
- Reduces image size
- Improves build speed
- Prevents secrets from leaking
- Avoids accidental inclusion of junk files

Common entries in `.dockerignore`:
- node_modules
- .git
- .env
- logs
- test files
- build artifacts

Think of `.dockerignore` as:
`.gitignore` for Docker builds

---

### 6. Exclude Build-Time Dependencies from Final Image (Multi-Stage Builds)

Many tools are required only to **build** the application, not to **run** it.

Examples:
- package.json, pom.xml
- npm, maven, gradle
- JDK (needed to compile Java, not to run it)
- Test frameworks
- Temporary build files

If these stay in the final image:
- Image size increases
- Security risk increases
- Runtime environment becomes messy

Solution:
**Multi-Stage Builds**

How it works conceptually:
- Stage 1 → build the app (heavy tools allowed)
- Stage 2 → runtime image (only final artifacts)

Only the **last stage** becomes the final image.

Result:
- Smaller images
- Cleaner runtime
- Better security

Multi-stage builds are a **must-use best practice**, not optional.

---

### 7. Never Run Containers as Root (Use Non-Root Users)

By default, containers run as **root** if no user is specified.

Why this is dangerous:
- Container root ≠ host root, but…
- Privilege escalation risks exist
- If container is compromised, damage potential increases
- Breaks security best practices

Best practice:
Create a dedicated user and group for the application.

Example approach:

    # Create group and user
    RUN groupadd -r tom && useradd -g tom tom

    # Set ownership and permissions
    RUN chown -R tom:tom /app

    # Switch to non-root user
    USER tom

    CMD node index.js

Benefits:
- Reduced blast radius
- Better isolation
- Aligns with least-privilege principle
- Stronger security posture

Running as root is almost never justified for application containers.

---

### 8. Scan Docker Images for Security Vulnerabilities

Even well-built images can contain:
- Known CVEs
- Vulnerable libraries
- Outdated dependencies

You should **scan images regularly**.

Docker provides:
Docker Scout

Command:
    docker scout cves <image-name>

Example:
    docker scout cves myapp:1.0

What this gives you:
- Known vulnerabilities (CVEs)
- Severity levels
- Actionable remediation info

Best practice:
- Scan during CI/CD
- Fail builds on critical vulnerabilities
- Rescan after base image updates

Security is not a one-time step — it’s continuous.

---

### 9. Putting All Best Practices Together (Mental Checklist)

Before shipping a Docker image, ask:

- Am I using an official base image?
- Am I pinning a specific version?
- Is my image size reasonable?
- Is Docker caching optimized?
- Is `.dockerignore` properly configured?
- Are build dependencies excluded?
- Is the container running as non-root?
- Has the image been vulnerability-scanned?

If all answers are yes → you’re doing Docker **the right way**.
