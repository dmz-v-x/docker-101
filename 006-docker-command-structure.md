## Docker Command Structure

### 1. What is a Docker command?

A Docker command is a **text-based instruction** that you type in the terminal to tell Docker **what you want to do**.

Examples of things you tell Docker using commands:

- Run a container
- Build an image
- List running containers
- Stop a container
- Remove images

All interaction with Docker (from terminal) happens through **Docker commands**.

---

### 2. Why understanding Docker command structure is important

Beginners often memorize commands without understanding their structure.

This leads to:
- Confusion
- Difficulty learning new commands
- Errors when combining flags and arguments

Once you understand the **structure**, every Docker command becomes predictable and readable.

---

### 3. Basic structure of a Docker command

The general structure of a Docker command is:

    docker [COMMAND] [SUBCOMMAND] [OPTIONS] [ARGUMENTS]

Not every command uses all parts, but this is the full mental model.

---

### 4. `docker` (Base command)

`docker` is the **base command**.

It tells the system:

> “I want to interact with Docker”

Without this keyword, the system does not know you are trying to use Docker.

Example:

    docker --version

Here:
- `docker` invokes Docker CLI
- `--version` is an option

---

### 5. COMMAND (High-level action)

The **COMMAND** tells Docker **what category of action** you want to perform.

Examples of Docker commands:

- `run` → run a container
- `build` → build an image
- `ps` → list containers
- `images` → list images
- `stop` → stop a container
- `rm` → remove a container

Example:

    docker run
    docker build
    docker ps

Think of COMMAND as **the main verb**.

---

### 6. SUBCOMMAND (More specific action)

Some Docker commands have **subcommands** to further specify what you want to do.

This is common in newer Docker CLI design.

Examples:

    docker image ls
    docker image rm
    docker container ps
    docker container stop

Here:
- `image` and `container` are subcommands
- They scope the command to a specific resource type

This improves clarity and consistency.

---

### 7. COMMAND vs SUBCOMMAND (Clear distinction)

Older style:

    docker ps
    docker images

Newer structured style:

    docker container ps
    docker image ls

Both work, but the second style is more explicit and readable.

---

### 8. OPTIONS (Flags that modify behavior)

OPTIONS are **flags** that change how a command behaves.

They usually start with:
- `-` (short option)
- `--` (long option)

Examples:

- `-d` → detached mode
- `-p` → port mapping
- `--name` → container name
- `-it` → interactive terminal

Example:

    docker run -d nginx

Here:
- `-d` modifies how the container runs

---

### 9. Short options vs long options

Short option:

    -d

Long option:

    --detach

Both mean the same thing, but long options are more readable.

Example:

    docker run --detach nginx

---

### 10. ARGUMENTS (What the command acts on)

Arguments are **values** that the command needs to operate.

Examples:
- Image name
- Container ID
- File path
- Port numbers

Example:

    docker run nginx

Here:
- `nginx` is an argument (image name)

Another example:

    docker stop abc123

Here:
- `abc123` is a container ID argument

---

### 11. Putting everything together

Command:

    docker run -d -p 5000:5000 online-store

Breakdown:

- `docker` → invoke Docker CLI
- `run` → command (run a container)
- `-d` → option (detached mode)
- `-p 5000:5000` → option with value (port mapping)
- `online-store` → argument (image name)

This reads as:

> “Docker, run a container in detached mode, map ports, using the online-store image”

---

### 12. Commands without subcommands

Some Docker commands are simple and do not use subcommands.

Examples:

    docker ps
    docker images
    docker version

These commands are self-contained.

---

### 13. Commands with subcommands

More explicit form:

    docker container ls
    docker image build
    docker network create
    docker volume inspect

Structure:

    docker [RESOURCE] [ACTION] [OPTIONS]

This style is preferred for clarity in large systems.

---

### 14. Multiple options in one command

You can pass **multiple options** in a single Docker command.

Example:

    docker run -it --rm ubuntu

Breakdown:

- `-i` → interactive
- `-t` → terminal
- `--rm` → remove container after exit
- `ubuntu` → image name

Options are processed from left to right.

---

### 15. Order of Docker command parts (Important rule)

General order:

1. docker
2. command / subcommand
3. options
4. arguments

Wrong ordering can cause errors.

Correct:

    docker run -d nginx

Incorrect:

    docker -d run nginx

---

### 16. How Docker parses commands internally

Internally Docker:

- Reads the command
- Identifies the action
- Applies options
- Validates arguments
- Sends request to Docker Daemon

If something is invalid:
- Docker CLI shows an error before execution

---

### 17. Getting help for any Docker command

Docker has built-in help.

General help:

    docker help

Command-specific help:

    docker run --help
    docker image --help

This shows:
- Available options
- Usage examples
- Expected arguments

---

### 18. Why Docker command structure is consistent

Docker maintains a consistent structure to:

- Make commands predictable
- Reduce learning curve
- Improve automation
- Support scripting and CI/CD

Once you learn one command, others feel familiar.

---

### 19. Common beginner mistakes

- Confusing options with arguments
- Wrong order of flags
- Forgetting required arguments
- Mixing old and new command styles
- Not reading `--help`

Understanding structure avoids these issues.

---

### 20. Final Summary

Docker command structure follows a clear pattern:

    docker [COMMAND] [SUBCOMMAND] [OPTIONS] [ARGUMENTS]

Where:

- `docker` invokes Docker
- `COMMAND` defines the action
- `SUBCOMMAND` adds specificity
- `OPTIONS` modify behavior
- `ARGUMENTS` provide targets or values

Once this structure is clear, **every Docker command becomes easy to read, write, and debug**.

---

### 21. What You Understand Now

You now understand:

- How Docker commands are structured
- Role of each command part
- How to read complex commands
- How to compose your own commands
- Why Docker CLI feels consistent

This understanding is essential before moving to:
- Advanced Docker commands
- Docker Compose syntax
- CI/CD automation
- Kubernetes command patterns
