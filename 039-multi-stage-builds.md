## Multi-Stage Builds in Docker

### 1. The Core Problem Before Multi-Stage Builds

Before multi-stage builds existed, Dockerfiles had a major limitation:

> Everything needed to build the application  
> also ended up inside the final image.

This caused problems such as:

- Very large images  
- Development tools shipped to production  
- Security risks  
- Slower deployments  

Let’s understand this problem clearly first.

---

### 2. What Is a Multi-Stage Build?

A **multi-stage build** is a Dockerfile that contains **multiple `FROM` instructions**.

Each `FROM`:

- Starts a new build stage  
- Has its own filesystem  
- Has its own purpose  

In simple words:

> Multi-stage builds let you separate **building** an application  
> from **running** an application.

Only the final stage becomes the production image.

---

### 3. Why Multi-Stage Builds Exist

The primary goal of multi-stage builds is:

> Create a **small, clean, secure production image**  
> without unnecessary build tools or source code.

Build tools are needed only during compilation or bundling — not at runtime.

---

### 4. How Multi-Stage Builds Work (Conceptually)

At a high level:

1. First stage  
	- Uses a full-featured image  
	- Installs dependencies  
	- Builds the application  

2. Second stage  
	- Uses a minimal image  
	- Copies only the final build artifacts  
	- Runs the application  

All intermediate stages are **discarded automatically**.

---

### 5. Why Multi-Stage Builds Are Useful

Multi-stage builds solve multiple problems at once.

---

### 5.1 Smaller Image Size

- Build tools are excluded  
- Source files are excluded  
- Only runtime artifacts remain  

Smaller images:

- Pull faster  
- Deploy faster  
- Use less storage  

---

### 5.2 Cleaner and More Secure Images

- No compilers in production  
- No package managers in production  
- Fewer attack vectors  

This improves security significantly.

---

### 5.3 Faster Build Times (With Caching)

- Dependency layers are cached  
- Only changed stages are rebuilt  
- CI pipelines run faster  

---

### 5.4 More Readable Dockerfiles

- Clear separation of concerns  
- Build logic vs runtime logic  
- Easier maintenance  

---

### 6. Single-Stage Dockerfile (The Problematic Approach)

Let’s look at a **single-stage Dockerfile** first.

Single-stage Dockerfile:

	FROM node:16 AS build
	WORKDIR /app
	COPY package.json /app/
	COPY package-lock.json /app/
	RUN npm install
	COPY . /app
	RUN npm run build
	EXPOSE 8080
	CMD ["npm", "start"]

---

### 7. Why This Single-Stage Dockerfile Is Not Ideal

This image contains:

- node_modules (development + production)  
- Source code  
- Build tools  
- Package manager  
- Build-time dependencies  

Problems:

- Large image size  
- Slower startup  
- Higher security risk  
- Unnecessary files in production  

We need a better approach.

---

### 8. Multi-Stage Dockerfile (Correct Approach)

Now let’s rewrite the same logic using a **multi-stage build**.

Stage 1: Build stage

	FROM node:16 AS build
	WORKDIR /app

	Copy package files and install dependencies
	COPY package.json package-lock.json /app/
	RUN npm install

	Copy the rest of the application code
	COPY . /app

	Build the application (e.g., transpile or bundle)
	RUN npm run build

Stage 2: Production stage

	FROM node:16-alpine
	WORKDIR /app

	Copy only required artifacts from the build stage
	COPY --from=build /app/dist /app/dist
	COPY --from=build /app/node_modules /app/node_modules

	EXPOSE 8080
	CMD ["npm", "start"]

---

### 9. Understanding the Build Stage

The first stage is named `build`.

Purpose:

- Build the application  
- Prepare final artifacts  

What happens here:

- Full Node.js image is used  
- Dependencies are installed  
- Source code is copied  
- Build process runs  

This stage:

- Is NOT part of the final image  
- Exists only during build time  

---

### 10. Understanding the Production Stage

The second stage is the **final image**.

Purpose:

- Run the application  
- Contain only what is needed at runtime  

What happens here:

- Smaller base image is used (`node:16-alpine`)  
- Only build output (`dist`)  
- Only runtime dependencies (`node_modules`)  
- No source files  
- No build tools  

This is the image that gets deployed.

---

### 11. The `COPY --from` Instruction (Key Concept)

This instruction is what makes multi-stage builds powerful.

	COPY --from=build /app/dist /app/dist

Meaning:

- Copy files  
- From the `build` stage  
- Into the current stage  

You can copy:

- Compiled binaries  
- Bundled JavaScript  
- Static assets  
- Anything you want  

---

### 12. What Gets Removed Automatically

Docker automatically removes:

- All intermediate stages  
- All unused layers  
- All build-only dependencies  

You don’t need cleanup commands.

---

### 13. Final Image Contents (Important Comparison)

| Single-Stage Image | Multi-Stage Image |
|-------------------|------------------|
| Build tools | ❌ |
| Source code | ❌ |
| Package managers | ❌ |
| Large base image | ❌ |
| Only runtime files | ✅ |
| Optimized for prod | ✅ |

---

### 14. Benefits of Multi-Stage Builds (Consolidated)

- Smaller image size  
- Faster deployments  
- Better security  
- Cleaner Dockerfiles  
- Clear separation of concerns  
- Professional-grade images  

Multi-stage builds are now the **industry standard**.

---

### 15. Common Real-World Use Cases

Multi-stage builds are used for:

- Node.js applications  
- Java applications (Maven/Gradle → JRE)  
- Go binaries  
- Frontend builds (React, Vue, Angular)  
- Microservices  

Anywhere compilation or bundling exists.

---

### 16. Common Beginner Mistakes

- Copying too many files into final stage  
- Forgetting to use a smaller base image  
- Not naming build stages  
- Using same image for all stages  
- Overcomplicating the Dockerfile  

---

### 17. Best Practices for Multi-Stage Builds

- Always use a minimal runtime image  
- Name build stages clearly  
- Copy only what is required  
- Keep stages simple  
- Combine with `.dockerignore`  
- Avoid copying source code into final stage  

---

### 18. Mental Model to Remember

- Stage 1 → Builder (heavy, disposable)  
- Stage 2 → Runner (light, permanent)  

Build stage is temporary.  
Production stage is what ships.
