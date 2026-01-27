## Key Docker Components 

### 1. Docker Engine

Docker Engine is the **core software that makes Docker work**.  
If Docker were a system, Docker Engine would be the **core machinery** running everything.

At the most basic level, Docker Engine is responsible for:

- Creating Docker images
- Running Docker containers
- Managing container lifecycle (start, stop, restart, delete)
- Managing images, networks, and volumes

Without Docker Engine, Docker does not exist.

---

#### 1.1 What exactly is Docker Engine?

Docker Engine is an application installed on your machine that provides:

- A container runtime
- APIs to communicate with Docker
- Tools to build and manage containers

It acts as the **bridge between your operating system and containers**.

---

#### 1.2 Internal parts of Docker Engine

Internally, Docker Engine includes:

- Docker Daemon
- Docker API
- Container runtime (containerd)

You don’t interact with these directly, but they work together behind the scenes.

---

### 2. Docker Daemon

Docker Daemon is the **background service** that actually does the work.

When Docker is running, the Docker Daemon is always running in the background.

---

#### 2.1 What does the Docker Daemon do?

Docker Daemon is responsible for:

- Building Docker images
- Running containers
- Pulling images from registries
- Managing networks and volumes

It listens for instructions and executes them.

---

#### 2.2 Why the Docker Daemon is important

When you run a Docker command:

- Your terminal does not run containers
- The daemon does

Containers continue running even if you close your terminal because the daemon runs independently.

---

### 3. Docker CLI (Command Line Interface)

Docker CLI is **how users communicate with Docker**.

It is a command-line tool that allows you to send instructions to Docker Engine.

---

#### 3.1 Role of Docker CLI

Docker CLI allows you to:

- Build images
- Run containers
- Stop containers
- Inspect Docker resources
- Remove images and containers

The CLI itself does not do the heavy work.

---

#### 3.2 How CLI and Daemon work together

Flow of a command:

- You type a command in the terminal
- Docker CLI sends the request to Docker Daemon
- Docker Daemon executes it
- Result is returned to the CLI

CLI = Interface  
Daemon = Executor  

---

#### 3.3 Example CLI commands

    docker images
    docker ps
    docker run nginx

These commands only work because the daemon is running.

---

### 4. Docker Desktop

Docker Desktop is a **developer-friendly application** that bundles everything needed to use Docker easily.

It is mostly used on:

- Windows
- macOS

---

#### 4.1 Why Docker Desktop exists

Docker was originally built for Linux.  
Windows and macOS do not run Linux containers natively.

Docker Desktop solves this by:

- Running a lightweight Linux VM internally
- Installing Docker Engine inside it
- Providing a GUI and system integration

---

#### 4.2 What Docker Desktop includes

Docker Desktop includes:

- Docker Engine
- Docker Daemon
- Docker CLI
- Docker Compose
- GUI dashboard

It is a **complete Docker environment** for developers.

---

#### 4.3 What Docker Desktop is NOT

Docker Desktop is:

- NOT Docker itself
- NOT required on Linux servers
- NOT used in production

It is a **local development tool**.

---

### 5. Docker Image

A Docker Image is a **read-only blueprint** used to create containers.

It contains:

- Application code
- Runtime (Node, Python, Java, etc.)
- Libraries
- OS-level dependencies
- Startup instructions

---

#### 5.1 Key properties of Docker Images

- Immutable (cannot be changed)
- Versioned
- Reusable
- Portable

Once built, an image never changes.

---

#### 5.2 Image as a mental model

Think of a Docker Image as:

- A template
- A snapshot
- A class (OOP analogy)

Images do not run on their own.

---

### 6. Docker Container

A Docker Container is a **running instance of a Docker Image**.

This is where the application actually executes.

---

#### 6.1 What a container provides

A container provides:

- Process isolation
- File system isolation
- Network isolation
- Environment isolation

But it still shares the host OS kernel.

---

#### 6.2 Image vs Container

- Image → Blueprint
- Container → Running application

One image can create multiple containers.

---

#### 6.3 Container lifecycle

    docker run nginx
    docker stop <container_id>
    docker rm <container_id>

- Run → Create & start container
- Stop → Stop execution
- Remove → Delete container

---

### 7. Dockerfile

A Dockerfile is a **text file containing instructions to build a Docker Image**.

It describes everything needed to create an image.

---

#### 7.1 Why Dockerfile is important

Without Dockerfile:

- Builds are manual
- Environments are inconsistent
- Errors are common

Dockerfile ensures:

- Automation
- Repeatability
- Consistency

---

#### 7.2 Common Dockerfile instructions

- FROM → Base image
- WORKDIR → Working directory
- COPY → Copy files
- RUN → Execute commands
- CMD → Start the app

---

#### 7.3 Example Dockerfile

    FROM node:18
    WORKDIR /app
    COPY package.json .
    RUN npm install
    COPY . .
    CMD ["node", "index.js"]

This creates an image capable of running a Node.js app.

---

### 8. Docker Registry

A Docker Registry is a **storage system for Docker images**.

It is where images are pushed and pulled from.

---

#### 8.1 Why Docker Registry is needed

Images must be:

- Shared across teams
- Used in CI/CD
- Pulled by servers

A registry solves this.

---

#### 8.2 Types of Docker Registries

- Public registries
- Private registries
- Cloud-managed registries

Docker Hub is the most common example.

---

### 9. Docker Hub

Docker Hub is a **public Docker Registry** provided by Docker.

It is the default registry used by Docker.

---

#### 9.1 What Docker Hub provides

- Millions of public images
- Private repositories
- Image versioning
- Easy sharing

---

#### 9.2 Typical team workflow with Docker Hub

- Build image locally
- Push image to Docker Hub
- Pull image in testing
- Pull same image in production

Same image everywhere.

---

### 10. Docker Compose

Docker Compose is a tool for **running multiple containers together**.

Real-world applications almost always need this.

---

#### 10.1 Why Docker Compose is required

Modern applications usually include:

- Backend service
- Database
- Cache
- Message broker

Running each manually is inefficient.

---

#### 10.2 What Docker Compose does

Docker Compose lets you:

- Define services in one file
- Start everything with one command
- Automatically manage networking

---

#### 10.3 Example Docker Compose file

    version: "3"
    services:
      web:
        image: node
        ports:
          - "3000:3000"
      db:
        image: postgres

One command:

    docker-compose up

Starts all services together.

---

### 11. How all Docker components fit together

End-to-end flow:

- Developer installs Docker Desktop
- Docker Engine runs via Docker Daemon
- Developer uses Docker CLI
- Dockerfile builds an image
- Image is pushed to Docker Registry
- Registry stores image (Docker Hub, ECR)
- Server pulls image
- Docker Engine creates container
- Docker Daemon manages runtime

