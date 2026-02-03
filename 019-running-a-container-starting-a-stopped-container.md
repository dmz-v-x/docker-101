## Running a Container and Starting a Stopped Container 

## PART 1: Running a Container

---

### 1. What does “running a container” mean?

Running a container means:

- Creating a new container from an image
- Setting up isolation (filesystem, network, process)
- Starting the main process inside the container

This happens when you use the `docker run` command.

Important:
- A **new container** is created every time
- Even if the image already exists

Think of it like:
> Creating and starting a brand-new instance of an application

---

### 2. Basic command to run a container

	docker run IMAGE_NAME

Example:

	docker run nginx

---

### 3. What `docker run` actually does internally

When you run:

	docker run nginx

Docker performs these steps automatically:

1. Checks if the image exists locally
2. Pulls the image from registry if missing
3. Creates a new container
4. Allocates filesystem and network
5. Starts the container process

This single command combines:
- docker pull
- docker create
- docker start

---

### 4. Running a container in detached mode

	docker run -d nginx

- `-d` means detached mode
- Container runs in background
- Terminal is freed immediately

Most server applications use detached mode.

---

### 5. Running a container with a name

	docker run --name my-nginx nginx

Naming containers makes management easier:

	docker stop my-nginx
	docker rm my-nginx

---

### 6. Running a container with port mapping

	docker run -d -p 8080:80 nginx

Meaning:
- Host port 8080
- Forwarded to container port 80

Now the app is accessible at:
- http://localhost:8080

---

### 7. Running a container with environment variables

	docker run -e ENV=production nginx

- `-e` injects environment variables
- Used for configuration at runtime

---

### 8. Running multiple containers from the same image

	docker run -d -p 8081:80 nginx
	docker run -d -p 8082:80 nginx

Same image, multiple isolated containers.

---

### 9. When should you use `docker run`?

Use `docker run` when:
- You want a **new container**
- You are testing an image
- You want multiple instances
- You don’t care about previous container state

---

## PART 2: Starting a Stopped Container

---

### 10. What does “starting a stopped container” mean?

Starting a stopped container means:

- Reusing an existing container
- Keeping its filesystem state
- Restarting its main process

No new container is created.

Think of it like:
> Turning a stopped machine back on

---

### 11. Command to start a stopped container

	docker start CONTAINER_NAME_OR_ID

Example:

	docker start my-nginx

---

### 12. What `docker start` does internally

When you run:

	docker start my-nginx

Docker:
1. Finds the existing container
2. Restores its filesystem
3. Reattaches network settings
4. Starts the original command

No image pull  
No container creation  

---

### 13. Starting multiple stopped containers

	docker start container1 container2 container3

Example:

	docker start web db cache

---

### 14. Starting all stopped containers

	docker start $(docker ps -a -q -f status=exited)

Useful for:
- Local development
- Restarting environments

---

### 15. Difference between `docker run` and `docker start`

| Feature | docker run | docker start |
|------|-----------|--------------|
| Creates new container | Yes | No |
| Uses existing container | No | Yes |
| Pulls image if missing | Yes | No |
| Keeps old container data | No | Yes |
| Typical use | First run | Restart |

This distinction is **extremely important**.

---

### 16. Common beginner mistakes

- Using `docker run` instead of `docker start`
- Creating duplicate containers unintentionally
- Losing data by recreating containers
- Confusing images with containers

Always ask:
> “Do I want a new container or the old one?”

---

### 17. Checking container status before starting

	docker ps -a

Look for:
- Exited containers → can be started
- Running containers → already active

---

### 18. Restarting a container (related command)

	docker restart CONTAINER_NAME_OR_ID

This is equivalent to:
- docker stop
- docker start

Useful for:
- Reloading configuration
- Applying changes

---

### 19. Local vs production usage

### Local environment
- Frequent run and start
- Many stopped containers
- Manual management

### Production environment
- Containers usually not restarted manually
- Orchestrators handle restarts
- docker start used rarely

---

### 20. Best practices

- Use docker run only for new containers
- Use docker start for existing ones
- Name containers explicitly
- Check container state before action
- Avoid recreating stateful containers
