## Installing Docker and Setting It Up Correctly 

### 1. What does “installing Docker” actually mean?

When we say **install Docker**, we are not installing just one thing.

Docker installation usually sets up:

- Docker Engine (core runtime)
- Docker Daemon (background service)
- Docker CLI (command-line tool)
- Supporting system services
- A system group called `docker`

All of these pieces must work together for Docker to function correctly.

---

### 2. Docker installation

Before commands, it’s important to understand **where Docker is being installed**.

#### Local development environment
- Laptop or personal machine
- Often uses Docker Desktop (Windows / macOS)
- On Linux, Docker Engine is installed directly
- Convenience and ease of use are prioritized

#### Production environment
- Usually Linux servers
- No Docker Desktop
- Only Docker Engine + Daemon
- Focus on stability, security, automation

This guide focuses on **Linux-style installation**, which applies to:
- Linux laptops
- Cloud servers
- Production systems

**For Installing Docker refer to [Docker Docs](https://docs.docker.com/engine/install/)**

---

### 3. Verifying Docker installation

Once Docker is installed, the **first check** is whether Docker CLI exists.

#### Command
    docker --version

#### What this command does
- Calls the Docker CLI binary
- Prints the installed Docker version
- Confirms Docker CLI is available in PATH

#### Example output
    Docker version 24.0.7, build 311b9ff

If this works:
- Docker CLI is installed correctly

If this fails:
- Docker is either not installed
- Or not installed correctly

---

### 4. Verifying Docker Daemon is running

Docker CLI alone is not enough.  
Docker needs a **running background service**.

#### Command
    docker info

#### What this command does
- Docker CLI sends a request to Docker Daemon
- Docker Daemon responds with system information
- Confirms daemon is running and accessible

#### What information it shows
- Number of containers
- Number of images
- Storage driver
- Cgroup driver
- Kernel version

If this command works:
- Docker Daemon is running
- CLI ↔ Daemon communication works

---

### 5. When Docker Daemon is NOT running

If `docker info` fails, it usually means:

- Docker Daemon is stopped
- Or user does not have permission to access it

---

### 6. Checking Docker Daemon status

Docker Daemon is managed by **systemd** on Linux.

#### Command
    sudo systemctl status docker

#### What this command does
- Asks systemd for Docker service status
- Shows whether Docker is:
  - Active (running)
  - Inactive (stopped)
  - Failed

If status shows **inactive**, Docker is not running.

---

### 7. Starting Docker Daemon manually

#### Command
    sudo systemctl start docker

#### What this command does
- Starts Docker Daemon immediately
- Does NOT persist after reboot

After running this:
- Docker Daemon starts
- `docker info` should work

---

### 8. Enabling Docker to start automatically

By default, services may not start after reboot.

#### Command
    sudo systemctl enable docker

#### What this command does
- Registers Docker service with system startup
- Ensures Docker starts automatically on boot

This is **very important for production servers**.

---

### 9. Understanding Docker permissions

This is one of the most confusing parts for beginners.

---

### 10. Why Docker needs special permissions

Docker Daemon:

- Runs as **root**
- Can:
  - Start containers
  - Mount file systems
  - Manage networks
  - Control system resources

Because of this:
- Accessing Docker Daemon is equivalent to root access

So Linux restricts who can talk to Docker Daemon.

---

### 11. Does Docker create a `docker` group automatically?

Yes ✅  
**Docker installation automatically creates a system group called `docker`.**

#### Why does Docker create this group?

Linux uses **groups** to control access.

Docker uses the `docker` group to:

- Allow non-root users to access Docker Daemon
- Avoid running `docker` commands with `sudo` every time
- Maintain controlled access to root-level operations

---

### 12. Default Docker behavior after installation

After installing Docker:

- Docker Daemon runs as root
- Docker socket belongs to:
  - User: root
  - Group: docker
- Only users in `docker` group can access Docker without `sudo`

That’s why this happens:

    docker ps
    permission denied

---

### 13. Adding your user to the docker group

To fix permission issues, we add the current user to the `docker` group.

#### Command
    sudo usermod -aG docker $USER

---

### 14. Breaking down this command

- `sudo` → run as administrator
- `usermod` → modify a user account
- `-a` → append (do not overwrite existing groups)
- `-G docker` → add to docker group
- `$USER` → current logged-in user

This command means:

> “Add my user to the docker group without removing existing group memberships”

---

### 15. Important gotcha after adding user to docker group

Changes do NOT apply immediately.

You must either:

- Log out and log back in  
OR  
- Restart the system  

Until then:
- Docker permissions may still fail

---

### 16. Security warning

Any user in the `docker` group:

- Effectively has root-level access
- Can mount system directories
- Can control the host system

Because of this:
- Production servers must restrict docker group access
- Never add untrusted users to docker group

---

### 17. Testing Docker installation with a basic container

Once permissions are correct, we test Docker.

---

### 18. Running a basic application container

#### Command
    docker run -d -p 5000:5000 online-store

---

### 19. Breaking down the `docker run` command

- `docker` → Docker CLI
- `run` → create and start a container
- `-d` → detached mode (run in background)
- `-p 5000:5000` → port mapping
  - Host port: 5000
  - Container port: 5000
- `online-store` → Docker image name

This means:

> “Run the `online-store` image in background and expose container port 5000 on host port 5000”

---

### 20. What happens internally when this command runs

Docker does the following:

- Checks if `online-store` image exists locally
- Pulls it from registry if missing
- Creates a container
- Assigns network namespace
- Maps ports
- Starts application process

---

### 21. Verifying running containers

#### Command
    docker ps

This shows:
- Running containers
- Container IDs
- Port mappings
- Status

---

### 22. Running multiple containers from the same image

This is one of Docker’s biggest strengths.

---

### 23. Running multiple instances

#### Commands
    docker run -d -p 5001:5000 online-store
    docker run -d -p 5002:5000 online-store
    docker run -d -p 5003:5000 online-store

---

### 24. What is happening here conceptually

- Same image: `online-store`
- Multiple containers created
- Each container:
  - Has its own isolated environment
  - Runs independently
- Different host ports map to same container port

This enables:
- Horizontal scaling
- Load balancing
- Testing multiple instances

---

### 25. Why container port stays same

Inside the container:
- Application listens on port 5000

Outside the container:
- Each instance gets a unique host port

Docker networking handles the mapping.

---

### 26. Production vs Local setup differences 

#### Local environment
- Docker Desktop or Docker Engine
- Manual commands
- Debug-friendly
- Single machine

#### Production environment
- Docker Engine only
- No Docker Desktop
- Often managed by:
  - CI/CD
  - Docker Compose
  - Kubernetes
- Strict permissions
- Automated restarts

---

### 27. Common setup gotchas

- Forgetting to start Docker Daemon
- Not enabling Docker on boot
- Permission issues with docker group
- Not logging out after adding user
- Port conflicts
- Running containers as root unintentionally

