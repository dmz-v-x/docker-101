## Dockerfile

### 1. What Is a Dockerfile?

A **Dockerfile** is a **plain text file** that contains a sequence of instructions used by Docker
to **build a Docker image automatically**.

In simple words:

> A Dockerfile is a recipe that tells Docker  
> how to create an image step by step.

Each instruction:
- Adds a layer to the image
- Is executed in order
- Produces a predictable, repeatable environment

---

### 2. Why Dockerfiles Exist (Core Problem They Solve)

Without Dockerfiles:
- You would manually install dependencies
- Configuration would differ per machine
- Bugs would appear only in production
- Environments would drift over time

Dockerfiles solve this by providing:
- Consistency
- Repeatability
- Automation
- Portability

Write once → run anywhere.

---

### 3. Dockerfile → Image → Container (Big Picture)

Understanding this flow is critical:

1. Dockerfile  
   → Instructions

2. docker build  
   → Creates an image

3. docker run  
   → Creates a container from the image

Important:
- Dockerfile is NOT executed at runtime
- Dockerfile is used only during image build

---

### 4. Command to Build an Image Using a Dockerfile

### Basic build command

	docker build -t my-app-node .

---

### Breaking this down

- docker build  
  → Tells Docker to build an image

- -t my-app-node  
  → Tags the image with a name

- .  
  → Build context (current directory)
  → Dockerfile must exist here

After this:
- An image named `my-app-node` is created
- It can be used to run containers

---

### 5. Anatomy of a Simple Dockerfile (Node.js Example)

Let’s start with a **real Dockerfile**, then break it down line by line.

    FROM node:14
    WORKDIR /usr/src/app
    COPY package*.json ./
    RUN npm install
    COPY . .
    EXPOSE 3000
    CMD ["npm", "start"]


This is a **classic production-style Dockerfile**.

---

### 6. FROM — Base Image (The Foundation)

### What FROM Does

	FROM node:14

FROM specifies:
- The base image
- The starting point of your image

Think of it as:
> “What environment do I start from?”

---

### Why Base Images Matter

The base image provides:
- OS libraries
- Runtime (Node, Python, Java, etc.)
- Pre-installed tooling

Examples:
- node:14
- python:3.9
- ubuntu:22.04

Every Dockerfile **must start with FROM** (except ARG-only cases).

---

### 7. WORKDIR — Working Directory Inside Container

	WORKDIR /usr/src/app

What this does:
- Sets the default directory for all future commands
- Creates the directory if it doesn’t exist

Why this matters:
- Avoids using absolute paths everywhere
- Keeps Dockerfile clean
- Improves readability

Equivalent to:
	cd /usr/src/app

But persistent across instructions.

---

### 8. COPY — Copy Files into the Image

	COPY package*.json ./

COPY:
- Copies files from host
- Into the image filesystem

This line copies:
- package.json
- package-lock.json (if present)

Why copy dependencies first?
→ Docker caching optimization (very important).

---

### 9. RUN — Execute Commands During Build

	RUN npm install

RUN:
- Executes commands at build time
- Installs dependencies
- Modifies the image

Important:
- RUN creates a new image layer
- Output becomes part of the image

This is where:
- apt-get install
- npm install
- pip install
happens.

---

### 10. COPY (Again) — Copy Application Code

	COPY . .

This copies:
- Entire project
- Into the container
- Excluding files in .dockerignore

This happens **after dependencies** to leverage caching.

---

### 11. EXPOSE — Document Container Ports

	EXPOSE 3000

EXPOSE:
- Documents which port the app listens on
- Does NOT actually publish the port
- Is informational + tooling-friendly

Actual exposure happens via:
- docker run -p

---

### 12. CMD — Default Runtime Command

	CMD ["npm", "start"]

CMD:
- Defines what runs when the container starts
- Can be overridden at runtime
- Usually starts the app

Only **one CMD** is effective.
If multiple exist → last one wins.

---

### 13. ADD vs COPY (Important Difference)

### COPY (Preferred)

- Simple
- Predictable
- Copies local files only

### ADD (Advanced)

- Can extract archives
- Can fetch URLs
- Less predictable

Example:

	ADD https://example.com/somefile.tar.gz /usr/src/app/

Best practice:
> Use COPY unless you specifically need ADD features.

---

### 14. ENV — Environment Variables

	ENV NODE_ENV=production
	ENV APP_PORT=3000

ENV:
- Sets environment variables
- Available at runtime
- Used by applications

Examples:
- Configuration
- Feature flags
- Runtime behavior

---

### 15. VOLUME — Declare Persistent Storage

	VOLUME /data

VOLUME:
- Creates a mount point
- Signals persistent data
- Often used for databases

Important:
- Automatically creates anonymous volumes
- Should be used carefully

---

### 16. ARG — Build-Time Variables

	ARG VERSION=1.0

ARG:
- Exists only during build
- Not available at runtime
- Used for build customization

Example:
- Versioning
- Feature toggles
- Conditional builds

ARG ≠ ENV (very important).

---

### 17. ENTRYPOINT — Fixed Startup Command

ENTRYPOINT defines:
- The main executable
- Cannot be overridden easily

Used when:
- Container is meant to behave like a command

---

### 18. Combining ENTRYPOINT and CMD (Correct Pattern)

    FROM python:3.9
    WORKDIR /app
    COPY . .
    RUN pip install -r requirements.txt
    ENTRYPOINT ["python"]
    CMD ["app.py"]


How this works:
- ENTRYPOINT → fixed command
- CMD → default arguments

At runtime:
	python app.py

CMD can be overridden:
	docker run my-image other.py

ENTRYPOINT remains fixed.

---

### 19. Dockerfile Execution Order (Very Important)

Docker executes instructions:
- Top to bottom
- One instruction = one layer
- Layers are cached

Changing one line:
- Invalidates cache for all lines below it

This affects:
- Build speed
- Image size
- Performance

---

### 20. Dockerfile Best Practices (Production-Grade)

---

### 20.1 Use Official Base Images

- Trusted
- Optimized
- Secure

Avoid random images.

---

### 20.2 Avoid latest Tag

Bad:
	FROM node:latest

Good:
	FROM node:18-alpine

Why:
- latest is unpredictable
- Can break builds

---

### 20.3 Leverage Docker Caching

- Copy dependency files first
- Install deps
- Then copy source code

This speeds up rebuilds massively.

---

### 20.4 Minimize Number of Layers

- Combine related RUN commands
- Avoid unnecessary instructions

Fewer layers → smaller images.

---

### 20.5 Avoid Installing Unnecessary Packages

Only install what you need.
Every package:
- Increases image size
- Increases attack surface

---

### 20.6 Use .dockerignore File

Prevent copying:
- node_modules
- logs
- secrets
- build artifacts

This:
- Speeds up builds
- Reduces image size
- Improves security

---

### 20.7 Setup Proper Permissions

- Avoid running as root when possible
- Use non-root users
- Reduce container privileges

---

### 20.8 Keep Dockerfile Simple and Readable

- Logical ordering
- Clear intent
- Minimal complexity

Simple Dockerfiles are easier to maintain.

---

### 20.9 Keep Image Size Small

- Use slim or alpine images
- Remove temporary files
- Optimize layers

Smaller images:
- Build faster
- Deploy faster
- Are more secure

---

### 21. Common Beginner Mistakes

- Putting everything in one RUN
- Using latest tag
- Copying entire filesystem
- Ignoring caching
- Hardcoding secrets
- Writing monolithic Dockerfiles

---

### 22. Mental Model to Remember

- Dockerfile = recipe
- Image = baked cake
- Container = cake being eaten

You change the recipe → bake again.
