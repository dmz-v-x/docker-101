## Distroless Images

### 1. What Are Distroless Images (Concept First)

Distroless images are a **container image design approach** where the image contains **only the application and the exact runtime dependencies required to run it** — nothing more.

That means:
- ❌ No Linux distribution (no Debian, Ubuntu, Alpine)
- ❌ No shell (bash, sh)
- ❌ No package managers (apt, apk, yum)
- ❌ No debugging utilities (curl, wget, vim, ps)
- ✅ Only the application + required runtime libraries

In short:

Distroless images remove *everything that is not required to run the application*.

This concept was introduced by **Google** as part of the *distroless* project to build **minimal, secure, production-grade containers**.

---

### 2. Why Traditional Images Are a Problem

Typical container images look like this:

- Base OS (Debian / Alpine)
- Package manager
- Shell
- System utilities
- Application runtime
- Application code

Problems with this approach:
- Large image size
- Bigger attack surface
- Attackers can install tools inside container
- More CVEs to patch
- Slower startup times

Most of this OS layer is **never used by the application**.

Distroless solves this inefficiency.

---

### 3. What Does “Distroless” Actually Mean?

“Distroless” does **not** mean:
- No OS at all

It means:
- No *general-purpose* OS distribution

A distroless image still contains:
- Kernel interaction via host
- Required shared libraries
- Language runtime (Node, Java, Python, etc.)

But it does **not** contain:
- Shell
- Init system
- Package manager
- Debugging tools

---

### 4. When Do Distroless Images Come Into the Picture?

Distroless images are **not for development**.

They are ideal when:
- You are building **final production images**
- Your app is already tested
- You don’t need interactive debugging
- You want maximum security and immutability

Typical lifecycle:
- Development → full image (node, python, ubuntu)
- Build → multi-stage build
- Production → distroless image

---

### 5. Why Should We Use Distroless Images?

#### 5.1 Security (Biggest Reason)

Because there is:
- No shell
- No package manager

An attacker:
- Cannot exec into the container and run commands
- Cannot install malware or tools
- Cannot easily explore the filesystem

This drastically reduces the **attack surface**.

---

#### 5.2 Smaller Image Size

Distroless images:
- Remove unnecessary OS files
- Remove unused binaries
- Contain only what’s needed

Results:
- Faster pulls
- Faster deployments
- Less storage usage

---

#### 5.3 Better Performance

Smaller images mean:
- Faster container startup
- Lower memory footprint
- Better cold-start times

Especially important in:
- Microservices
- Serverless-style workloads

---

#### 5.4 Immutable Infrastructure

Distroless images enforce immutability:
- Nothing can be installed at runtime
- Container behavior is fixed
- Changes require rebuilding the image

This aligns perfectly with **immutable infrastructure principles**.

---

#### 5.5 Compliance and Auditing

Fewer components mean:
- Fewer CVEs
- Easier audits
- Easier compliance reporting
- Clear understanding of what runs in production

---

### 6. Distroless vs Alpine vs Debian (Mental Model)

| Feature | Debian | Alpine | Distroless |
|------|-------|--------|------------|
| Shell available | Yes | Yes | No |
| Package manager | Yes | Yes | No |
| Debug tools | Yes | Limited | No |
| Image size | Large | Small | Smallest |
| Security surface | High | Medium | Very low |
| Dev-friendly | Yes | Yes | No |
| Production-ready | Yes | Yes | **Best** |

Distroless is **the final stage**, not the starting point.

---

### 7. Types of Distroless Images

Distroless provides language/runtime-specific images.

#### 7.1 Static binaries
- gcr.io/distroless/static
- Used for Go, Rust (statically compiled)

#### 7.2 Base image
- gcr.io/distroless/base
- Minimal libc-based runtime

#### 7.3 Language-specific images
- gcr.io/distroless/python3
- gcr.io/distroless/nodejs
- gcr.io/distroless/java

These include:
- Required runtime
- Required shared libraries
- Nothing else

---

### 8. Distroless Images and Multi-Stage Builds (Very Important)

Distroless images are almost **always used with multi-stage builds**.

Why?
- You need build tools (npm, pip, compilers)
- Distroless images cannot install anything

So the pattern is:
- Stage 1 → build with full image
- Stage 2 → copy artifacts into distroless image

---

### 9. Node.js Distroless Example (Step-by-Step)

#### 9.1 Build Stage (Full Image)

	FROM node:18 AS build
	WORKDIR /app
	COPY package*.json ./
	RUN npm install
	COPY . .
	RUN npm run build

What happens:
- Full Node.js environment
- Dependencies installed
- App built
- This stage is disposable

---

#### 9.2 Production Stage (Distroless)

	FROM gcr.io/distroless/nodejs18
	WORKDIR /app
	COPY --from=build /app /app
	CMD ["app.js"]

What happens:
- No shell
- No npm
- No OS utilities
- Only Node runtime + app code

This is the final production image.

---

### 10. How Do You Run a Distroless Container?

Same as any other image:

	docker run -d my-distroless-app

But remember:
- docker exec -it container bash ❌ will NOT work
- There is no shell inside

This is expected behavior.

---

### 11. How Do You Debug Distroless Images?

Debugging requires a mindset shift.

#### 11.1 Debug Using a Separate Debug Image

Common pattern:
- app:debug → normal image with shell
- app:prod → distroless image

Debug locally using debug image.

---

#### 11.2 Use Sidecar Containers

Attach:
- busybox
- alpine
- debug container

To the same network or pod (Kubernetes) for inspection.

---

#### 11.3 Rely on Logs and Metrics

With distroless:
- Logs become critical
- Health endpoints become important
- Observability matters

Production debugging should rely on:
- Logs
- Metrics
- Traces

Not shells.

---

### 12. Distroless in Kubernetes (Common Use)

Distroless images are extremely common in Kubernetes because:
- Containers should be immutable
- Debugging is externalized
- Security is critical

kubectl exec is limited:
- Works only if app itself supports it
- No shell available

This is intentional.

---

### 13. Caveats and Trade-offs

Distroless images are powerful, but not free.

#### 13.1 Harder Debugging
- No shell
- No tools
- Requires better observability

#### 13.2 Not for Development
- Slows iteration
- Poor DX for debugging

#### 13.3 Requires Proper Build Pipelines
- Multi-stage builds required
- CI/CD must be solid

---

### 14. When NOT to Use Distroless

Avoid distroless when:
- You are still developing
- You frequently exec into containers
- You rely on runtime tools
- You need quick hotfixes inside containers

Use distroless only when the app is **production-ready**.

---

### 15. Best Practices for Using Distroless Images

- Always use multi-stage builds
- Keep build and runtime stages separate
- Add strong logging
- Add health checks
- Use minimal permissions
- Never add debugging tools to production images

---

### 16. Mental Model to Remember

- Full image → for building and debugging
- Distroless image → for running in production

If you try to debug production by exec-ing into containers,
you are already doing it wrong.


