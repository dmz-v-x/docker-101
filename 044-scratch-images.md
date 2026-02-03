## Scratch Images

### 1. What Is a Scratch Image?

`scratch` is a **special, reserved Docker base image** that represents **an empty image**.

It is not a real Linux distribution.

It contains:
- ❌ No operating system
- ❌ No shell
- ❌ No package manager
- ❌ No system libraries
- ❌ No utilities
- ❌ No filesystem (other than what you copy in)

In simple words:

`scratch` = **nothing at all**

Docker literally starts from an empty tarball.

---

### 2. Why Does Docker Even Provide `scratch`?

`scratch` exists for one purpose:

> To allow you to build **fully self-contained container images** from absolute zero.

It is designed for applications that:
- Do not rely on any OS utilities
- Do not require dynamic libraries
- Can run as a single executable file

---

### 3. When Do Scratch Images Come Into Picture?

Scratch images are used when:
- You are building **statically compiled binaries**
- Your application does not need a runtime environment
- You want the smallest and most secure container possible

This is common in:
- Go
- Rust
- C / C++

Scratch is almost never used with:
- Node.js
- Python
- Java

---

### 4. Why Use Scratch Images?

Scratch images exist to solve **extreme optimization and security** problems.

---

#### 4.1 Smallest Image Possible

Because the image starts empty:
- Final image size can be **a few megabytes or less**
- Only your binary is present

This is the smallest Docker image you can build.

---

#### 4.2 Zero Attack Surface

There is:
- No shell to exploit
- No libraries to hijack
- No tools to misuse
- No package manager to install malware

If an attacker breaks in:
- There is literally nothing to run

This is **maximum security by design**.

---

#### 4.3 Full Control Over Image Contents

With scratch:
- You explicitly choose every file
- Nothing sneaks in implicitly
- No hidden dependencies

This is ideal for high-security environments.

---

#### 4.4 Fully Portable Applications

All dependencies are bundled into the binary.

That means:
- No runtime surprises
- No missing libraries
- No OS differences

If the binary runs on Linux → it runs in scratch.

---

### 5. What Are the Limitations of Scratch Images?

Scratch images are powerful, but very restrictive.

---

#### 5.1 No Debugging at All

You cannot:
- docker exec bash
- run ls
- inspect files inside
- install debug tools

Debugging relies entirely on:
- Logs
- Metrics
- Exit codes

---

#### 5.2 No OS Layer or Runtime Tools

There is:
- No libc
- No openssl
- No CA certificates
- No timezone data

If your app needs these, you must **add them manually**.

---

#### 5.3 Only Suitable for Static Binaries

Scratch requires:
- Everything compiled into one executable
- No dynamic linking
- No runtime interpreter

This makes it unsuitable for most dynamic languages.

---

### 6. Scratch vs Distroless vs Alpine vs Slim (Big Picture)

| Feature | Scratch | Distroless | Alpine | Slim |
|------|--------|-----------|--------|------|
| Base OS | ❌ None | ✅ Minimal runtime | ✅ Alpine Linux | ✅ Debian-based |
| Shell | ❌ No | ❌ No | ✅ Yes | ✅ Yes |
| Package manager | ❌ No | ❌ No | ✅ apk | ✅ apt |
| Image size | ✅ Smallest (~1MB) | ✅ Very small (~20MB) | ✅ Small (~5MB) | ✅ Small (~20–50MB) |
| Security | ✅ Highest | ✅ Very high | ✅ Good | ✅ Fair |
| Debugging | ❌ Impossible | ❌ Hard | ✅ Possible | ✅ Easy |
| Suitable languages | Go, Rust, C | Node, Python, Java | Most | Most |

---

### 7. When Should You Use Scratch?

Use scratch when **all** of the following are true:

- Your app is a single static binary
- You want the smallest possible image
- You want the lowest attack surface
- You do not need a shell
- You are comfortable debugging via logs

Scratch is a **specialist tool**, not a general solution.

---

### 8. Why Node.js and Python Don’t Work with Scratch

Node.js and Python:
- Are interpreters
- Require runtime environments
- Depend on dynamic libraries
- Expect system-level support

They cannot easily be compiled into a single static binary.

So:
- Node.js ❌ not suitable for scratch
- Python ❌ not suitable for scratch

This is why distroless exists for these languages.

---

### 9. Why Go Works Perfectly with Scratch

Go was designed for:
- Static compilation
- Self-contained binaries
- Minimal runtime dependencies

When you run:

	go build -o myapp

Go produces:
- One binary
- Including Go runtime
- Including all dependencies

No external libraries required.

---

### 10. How Scratch Works with Go (Step-by-Step)

#### 10.1 Build Stage (Full Go Environment)

	FROM golang:1.21 AS builder
	WORKDIR /app
	COPY . .
	RUN go build -o myapp

This stage:
- Has Go compiler
- Downloads dependencies
- Produces static binary

---

#### 10.2 Final Stage (Scratch)

	FROM scratch
	COPY --from=builder /app/myapp /
	ENTRYPOINT ["/myapp"]

This stage:
- Starts from nothing
- Copies only the binary
- Runs it directly

Final image contains:
- Just `/myapp`
- Nothing else

---

### 11. How You Run a Scratch Container

	docker run my-scratch-app

That’s it.

No shell.  
No tools.  
Only your application.

---

### 12. Debugging Strategy for Scratch Images

Because debugging inside is impossible:

- Log everything clearly
- Fail loudly
- Expose health endpoints
- Use structured logs
- Debug in a non-scratch image first

Scratch is **not the place to debug**.

---

### 13. Scratch in Production (Where It Shines)

Scratch is ideal for:
- High-security systems
- Infrastructure tools
- Internal services
- Kubernetes workloads
- Performance-critical services

Many cloud-native Go services use scratch.

---

### 14. When NOT to Use Scratch

Avoid scratch if:
- You need interactive debugging
- Your app needs system libraries
- Your app uses dynamic languages
- You’re early in development

Scratch is **the final destination**, not the starting point.

---

### 15. Mental Model to Remember

- Slim → development friendly
- Alpine → minimal OS
- Distroless → minimal runtime
- Scratch → absolute zero

Each level removes more layers.

  
