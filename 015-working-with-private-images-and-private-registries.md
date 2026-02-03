## Working with Private Docker Images and Pushing Images to Public or Private Registries  

### 1. What is a Docker Registry?

A **Docker Registry** is a service that stores Docker images and allows them to be distributed.

In simple terms:
- Your machine builds images
- A registry stores those images
- Other machines pull those images from the registry

A registry is to Docker images what GitHub is to source code.

Common registries:
- Docker Hub (public + private)
- AWS Elastic Container Registry (ECR) (private)
- GitHub Container Registry
- GitLab Container Registry

---

### 2. Public Images vs Private Images

### Public Images
- Anyone can pull them
- No authentication required
- Used for open-source software

Examples:
- node
- nginx
- postgres

---

### Private Images
- Require authentication
- Access is restricted
- Used for proprietary or internal applications

Private images are the **default choice in production systems**.

---

### 3. Why Private Images Are Important

Private images are used because:
- Application source code is confidential
- Internal services should not be exposed publicly
- Security and compliance requirements exist
- Controlled access is required in production

In real-world systems, most production images are private.

---

### 4. Universal Image Push Workflow (Applies Everywhere)

No matter which registry you use, the workflow is always the same:

1. Build the image locally
2. Tag the image for the target registry
3. Authenticate with the registry
4. Push the image
5. Pull the image where needed

Only the **registry URL and authentication method change**.

---

### 5. Generic Syntax (Registry-Agnostic)

### 5.1 Build an image

	docker build -t IMAGE_NAME:TAG .

---

### 5.2 Tag image for a registry

	docker tag SOURCE_IMAGE REGISTRY_URL/REPOSITORY:TAG

---

### 5.3 Login to registry

	docker login REGISTRY_URL

---

### 5.4 Push image

	docker push REGISTRY_URL/REPOSITORY:TAG

---

### 5.5 Pull image

	docker pull REGISTRY_URL/REPOSITORY:TAG

Remember this pattern. Everything else is just a specialization of this.

---

## PART A: PUSHING IMAGES TO DOCKER HUB

---

### 6. Docker Hub Basics

Docker Hub is the default Docker registry.

It supports:
- Public repositories
- Private repositories

Docker Hub image naming format:

	USERNAME/REPOSITORY:TAG

Example:

	himanshu/online-store:v1

---

### 7. Step 1: Create Docker Hub Account and Repository

Before pushing:
- Create a Docker Hub account
- Create a repository (public or private)

Repository example:
- online-store

---

### 8. Step 2: Build Image Locally

	docker build -t online-store:v1 .

This creates a local image named:

	online-store:v1

---

### 9. Step 3: Tag Image for Docker Hub

Docker Hub requires your username in the image name.

	docker tag online-store:v1 himanshu/online-store:v1

Explanation:
- online-store:v1 → local image
- himanshu/online-store:v1 → Docker Hub target

---

### 10. Step 4: Login to Docker Hub

	docker login

You will be prompted for:
- Docker Hub username
- Password or access token

Docker stores credentials locally after login.

---

### 11. Step 5: Push Image to Docker Hub

	docker push himanshu/online-store:v1

Docker will:
- Upload image layers
- Skip already existing layers
- Push metadata

---

### 12. Public vs Private Repositories on Docker Hub

### Public Repository
- Anyone can pull the image

	docker pull himanshu/online-store:v1

---

### Private Repository
- Only authenticated users can pull
- docker login is mandatory

---

### 13. Common Docker Hub Mistakes

- Forgetting to include username in tag
- Not logging in before pushing
- Using latest tag in production
- Pushing secrets inside images
- Assuming Docker Hub private repos are unlimited

---

## PART B: PUSHING IMAGES TO AWS ECR (PRIVATE REGISTRY)

---

### 14. What is AWS ECR?

AWS Elastic Container Registry (ECR) is:
- Fully managed by AWS
- Private by default
- Integrated with AWS IAM
- Widely used in production systems

ECR images are not public unless explicitly configured.

---

### 15. AWS ECR Image Naming Format

	AWS_ACCOUNT_ID.dkr.ecr.REGION.amazonaws.com/REPOSITORY:TAG

Example:

	123456789012.dkr.ecr.ap-south-1.amazonaws.com/online-store:v1

---

### 16. Step 1: Create an ECR Repository

Create an ECR repository using:
- AWS Console
- AWS CLI
- Infrastructure as Code

Repository name example:
- online-store

AWS provides a **repository URI** after creation.

---

### 17. Step 2: Authenticate Docker with AWS ECR

AWS uses short-lived authentication tokens.

	aws ecr get-login-password --region ap-south-1 | \
	docker login --username AWS \
	--password-stdin 123456789012.dkr.ecr.ap-south-1.amazonaws.com

Explanation:
- AWS CLI generates a temporary token
- Docker uses that token to log in
- Token expires automatically

---

### 18. Step 3: Build Image Locally

	docker build -t online-store:v1 .

---

### 19. Step 4: Tag Image for AWS ECR

	docker tag online-store:v1 \
	123456789012.dkr.ecr.ap-south-1.amazonaws.com/online-store:v1

This tells Docker that the image belongs to ECR.

---

### 20. Step 5: Push Image to AWS ECR

	docker push 123456789012.dkr.ecr.ap-south-1.amazonaws.com/online-store:v1

Docker will:
- Upload image layers
- Register the image in ECR
- Make it available to AWS services

---

## 21. Pulling Image from AWS ECR

On any machine:
- IAM permission is required
- Authentication must be performed again

	docker pull 123456789012.dkr.ecr.ap-south-1.amazonaws.com/online-store:v1

---

### 22. Docker Hub vs AWS ECR (Comparison)

| Feature | Docker Hub | AWS ECR |
|------|----------|--------|
| Public images | Yes | Limited |
| Private by default | No | Yes |
| Authentication | Username / Token | AWS IAM |
| Production usage | Common | Very common |
| CI/CD integration | Good | Excellent |
| Cost model | Repo-based | Storage + transfer |

---

### 23. Security Best Practices (Very Important)

Never:
- Bake secrets into images
- Use latest tag in production
- Share private registry URLs publicly
- Grant wide IAM permissions

Always:
- Use versioned tags
- Scan images before pushing
- Use non-root containers
- Rotate credentials
- Restrict registry access

---

### 24. Local vs Production Registry Usage

### Local / Learning Environment
- Docker Hub public images
- Manual login
- Flexible tagging

---

### Production Environment
- Private registries
- CI/CD-controlled pushes
- Immutable image tags
- Automated scanning
- IAM-based access

---

### 25. Real-World CI/CD Image Flow

1. Build image
2. Tag with version or commit hash
3. Scan image for vulnerabilities
4. Push to private registry
5. Deploy using exact tag
6. Roll back using previous tag if needed

In production, the registry is the **single source of truth**.




