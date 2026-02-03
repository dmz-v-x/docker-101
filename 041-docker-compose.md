## Docker Compose

### 1. Docker Compose — Quick overview

Docker Compose is a tool to **define and run multi-container applications** using a single YAML file (usually `docker-compose.yml`).  
Think of it as a single declarative place to configure your app’s services, networks and volumes — then start/stop the whole app with a single command.

---

### 2. When to use Docker Compose

#### 2.1 Use Compose when:
- Your app requires multiple containers (web + db + cache + worker).
- You want reproducible local environments.
- You want simple scripts for CI/dev to start the full stack.
- You want to describe networks and volumes declaratively.

---

### 3. Compose file fundamentals (structure)

A minimal compose file contains:

#### 3.1 Top-level keys
- version (optional with modern compose v2, but commonly used)
- services (required) — list of app services
- networks (optional)
- volumes (optional)

#### 3.2 Basic example (explained line-by-line)
	# docker-compose.yml
	version: '3.8'
	services:
	  web:
	    image: nginx:alpine
	    ports:
	      - "8080:80"
	    volumes:
	      - ./html:/usr/share/nginx/html
	    networks:
	      - app-network

	  app:
	    image: node:14
	    environment:
	      - DATABASE_URL=mysql://db:3306/mydb
	    depends_on:
	      - db
	    networks:
	      - app-network

	  db:
	    image: mysql:5.7
	    environment:
	      - MYSQL_ROOT_PASSWORD=rootpassword
	      - MYSQL_DATABASE=mydb
	    volumes:
	      - db_data:/var/lib/mysql
	    networks:
	      - app-network

	networks:
	  app-network:
	    driver: bridge

	volumes:
	  db_data:

- `services:` defines containers (web, app, db).  
- `ports:` maps host:container ports.  
- `volumes:` mounts persistent storage.  
- `depends_on:` expresses start-up order (note: not full health dependency).  
- `networks:` groups services on an isolated network.

---

### 4. Common service options and what they do

#### 4.1 image
- Pre-built image to use (docker hub or private registry).

#### 4.2 build
- Build an image from a local Dockerfile.
	# Example: build from current dir
	app:
	  build:
	    context: .
	    dockerfile: Dockerfile

#### 4.3 ports
- Expose container ports to host.
	- "8080:80" maps host 8080 → container 80

#### 4.4 volumes
- Persist or share data.
	- ./host/path:/container/path (bind mount)
	- db_data:/var/lib/mysql (named volume)

#### 4.5 environment / env_file
- Set env vars inline or via .env file.
	environment:
	  - NODE_ENV=production
	# or use .env with DATABASE_URL=${DATABASE_URL}

#### 4.6 depends_on
- Controls start order only; does not wait for service readiness.
	- To wait for healthy state use healthcheck + condition (v2.2+ features vary)

#### 4.7 networks
- Attach services to networks defined under `networks:`.

#### 4.8 deploy (Swarm only)
- Compose supports `deploy` for swarm mode (replicas, placement, resources). Not used by docker-compose in single-host mode.

#### 4.9 restart
- Configure restart policy (`no`, `always`, `on-failure`, `unless-stopped`).

#### 4.10 healthcheck
- Define how Compose can check a container’s health. Useful with scripts that wait for healthy state before starting dependent services.

---

### 5. Useful CLI commands (most common workflows)

#### 5.1 Start the stack (attached)
	docker-compose up
- Starts, builds (if needed), streams logs for all services.

#### 5.2 Start in background (detached)
	docker-compose up -d

#### 5.3 Rebuild images then start
	docker-compose up --build -d

#### 5.4 Stop and remove everything created by up
	docker-compose down
- To remove volumes as well:
	docker-compose down --volumes

#### 5.5 Scale a service
	docker-compose up --scale backend=3 -d
- Create multiple containers for `backend` service.

#### 5.6 View logs
	docker-compose logs
- Follow logs for single service:
	docker-compose logs -f web

#### 5.7 See running services/containers
	docker-compose ps

#### 5.8 Execute a command inside a running service container
	docker-compose exec app /bin/bash
- Useful for debugging (interactive shell).

#### 5.9 Run a one-off command in a service container (without long-running container)
	docker-compose run --rm app python manage.py migrate
- `run` creates a fresh container for the command; `exec` runs inside existing container.

#### 5.10 Build images only
	docker-compose build

#### 5.11 Pull images
	docker-compose pull

#### 5.12 Remove stopped service containers
	docker-compose rm

#### 5.13 Convert/validate compose file
	docker-compose config

---

### 6. Lifecycle: what docker-compose up actually does (step-by-step)

1. Parse `docker-compose.yml` and `.env`.  
2. Build images that are configured with `build:` (if not present).  
3. Pull images defined by `image:` from registries (if not present locally).  
4. Create networks defined under `networks:` (if missing).  
5. Create volumes defined under `volumes:` (if missing).  
6. Create containers for each service (but not necessarily start dependent ones until created).  
7. Start containers in dependency order (in practice, depends_on affects create/start order but not readiness).  
8. Attach and stream logs (unless `-d`).

---

### 7. Environment variables and .env files

#### 7.1 .env usage
- Compose automatically loads `.env` from the same directory as the compose file.
- Use `${VAR}` in `docker-compose.yml` to substitute values.

	# .env
	DATABASE_URL=postgres://user:pass@db:5432/mydb
	MYSQL_ROOT_PASSWORD=secret

	# docker-compose.yml excerpt
	  app:
	    environment:
	      - DATABASE_URL=${DATABASE_URL}

#### 7.2 env_file
- You can also specify env files per-service:
	services:
	  app:
	    env_file:
	      - ./app.env

---

### 8. Volumes in Compose (persistence and sharing)

#### 8.1 Declare volumes
	volumes:
	  db_data:

#### 8.2 Use volumes in services
	services:
	  db:
	    volumes:
	      - db_data:/var/lib/mysql

#### 8.3 Named volumes vs bind mounts
- Named volume (managed by Docker): db_data:/path
- Bind mount (host folder): ./html:/usr/share/nginx/html

#### 8.4 Cleanup
- Volumes persist beyond `docker-compose down` unless you pass `--volumes`. Use `docker volume ls` and `docker volume prune` for cleanup.

---

### 9. Networks in Compose

#### 9.1 Default network
- Compose creates a default network for the project if you do not define custom networks.

#### 9.2 User-defined networks
	networks:
	  app-network:
	    driver: bridge

- Attach services to networks with `networks:` under each service.

#### 9.3 Service discovery / DNS
- Services can reach each other by service name (e.g., `db`) thanks to built-in DNS.

---

### 10. Service scaling, load balancing and patterns

#### 10.1 Horizontal scale
- `docker-compose up --scale service=X` will create X container instances for that service.

#### 10.2 Internal load balancing
- The Docker network will round-robin DNS, but for HTTP, use a reverse proxy (Nginx, Traefik) to distribute traffic across replicas.

	# Example upstream block for Nginx (conceptual)
	upstream backend {
	    server backend_1:3000;
	    server backend_2:3000;
	    server backend_3:3000;
	}

#### 10.3 Sticky sessions & session store
- For stateful sessions, use centralized session store (Redis) when scaling web app replicas.

---

### 11. depends_on and service health

#### 11.1 depends_on
- Only controls start order (service A will start after service B is started).  
- It **does not** wait for the dependent service to be healthy or ready to accept connections.

#### 11.2 waiting for readiness
- Combine `healthcheck:` in the dependent service and implement an entrypoint script in the dependant that polls the other service or use tools like `wait-for-it` or `dockerize`.

	# healthcheck example
	services:
	  db:
	    image: mysql
	    healthcheck:
	      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
	      interval: 10s
	      timeout: 5s
	      retries: 5

---

### 12. Compose and build optimizations

#### 12.1 Build context
- `build.context` defines the directory sent to Docker daemon; keep context small using `.dockerignore`.

#### 12.2 Caching and build args
- Use `build.args` to pass build-time args; avoid invalidating cache unnecessarily.

	services:
	  app:
	    build:
	      context: .
	      args:
	        - NODE_ENV=production

---

### 13. Compose file variants and overrides

#### 13.1 docker-compose.override.yml
- Compose automatically uses `docker-compose.override.yml` (if present) to override settings — useful for local development vs production differences.

#### 13.2 Multiple compose files
- You can provide multiple files and merge them:

	docker-compose -f docker-compose.yml -f docker-compose.prod.yml up -d

---

### 14. Compose v1 vs Compose v2 (CLI name difference)

- Historically command was `docker-compose`. Newer Docker integrates Compose v2 as `docker compose` (space). Both accept the same YAML in most cases, but use the CLI available on your system.
- If you see `docker compose` use that; if `docker-compose` is installed, either may work depending on your Docker setup.

---

### 15. Common troubleshooting commands

#### 15.1 See logs for all services
	docker-compose logs -f

#### 15.2 Inspect container created by Compose
	docker-compose ps
	docker inspect <container_id>

#### 15.3 Show effective config (merged)
	docker-compose config

#### 15.4 Remove a failed service container and restart
	docker-compose rm -s -v service_name
	docker-compose up -d service_name

---

### 16. CI / production notes

#### 16.1 Use Compose for local dev and simple QA environments.
#### 16.2 For production use, consider:
- Kubernetes or Docker Swarm for orchestration at scale.
- Use Compose to generate configs or for single-host production only with caution.
- Build images in CI and push to registry, then `docker-compose pull` + `docker-compose up -d` on target host.

---

### 17. Security and best practices with Compose

- Avoid embedding secrets in `docker-compose.yml`.
- Use environment variables from the host or secret management (Docker secrets in Swarm, Vault).
- Use networks to isolate services and limit published ports.
- Limit container privileges and use non-root users where possible.

---

### 18. Example advanced compose features (quick glance)

#### 18.1 Profiles (selectively start services)
	services:
	  debug:
	    image: some-debug-image
	    profiles: ["dev"]

Start only services in profile:
	docker-compose --profile dev up

#### 18.2 Resource limits (when supported / in deploy for swarm)
	deploy:
	  resources:
	    limits:
	      cpus: '0.50'
	      memory: 512M

(Note: `deploy` is for swarm; Compose local ignore deploy for non-swarm.)

---

### 19. Example common workflows (step-by-step)

#### 19.1 Local dev on a new machine
	# 1. Clone repo
	# 2. Create .env with secrets
	# 3. docker-compose up -d
	# 4. docker-compose exec app /bin/bash to run migrations
	# 5. docker-compose logs -f to view logs

#### 19.2 Rebuild after code changes
	docker-compose up --build -d

#### 19.3 Tear down everything including volumes
	docker-compose down --volumes

#### 19.4 Add a new replica to a service
	docker-compose up --scale worker=5 -d

---

### 20. Final summary and mental model

- Docker Compose = a single YAML that defines multi-container apps.  
- Use `docker-compose up/down` to manage full-application lifecycle.  
- Use `build`, `image`, `volumes`, `networks`, `env` to declare everything.  
- Use `.env` and `docker-compose.override.yml` for environment-specific config.  
- For scale and production, combine Compose with CI-built images and an orchestrator if needed.
