# Notes
These repo us made to track the knowledge 




Topics

-K8s command to work with the terminal 


#
-Docker
📦 Version 27.x  |  Complete Beginner's Guide
Master Docker
From Zero
Everything you need to understand containers, images, networking, and deployment — explained simply with real examples.

8
Topics Covered
50+
Code Examples
100%
Beginner Friendly
01
💡
What is Docker?
Docker is a tool that lets you package your application and all its dependencies into a single unit called a container. Think of it like a shipping container — it holds everything the app needs, and can run anywhere, on any computer, consistently.

🧠
Simple Analogy: Imagine you're baking a cake. Without Docker, you'd need to make sure your friend's kitchen has exactly the same oven, ingredients, and tools. With Docker, you ship them the entire kitchen in a box — they just open it and bake.

📦
Container
A running instance of an image. Like a live, running kitchen. Isolated, lightweight, fast to start.

🖼️
Image
A read-only blueprint/template. Like a recipe card. Used to create containers. Stored in registries.

🗄️
Registry
A storage hub for images (Docker Hub, AWS ECR). Like an app store — you pull images from here.

⚙️
Docker Engine
The runtime that builds and runs containers. The engine behind everything — installed on your machine.

📄
Dockerfile
A text file with instructions to build an image. Like a recipe — step-by-step instructions.

🎼
Docker Compose
A tool to run multiple containers together. Define your whole app stack in one YAML file.

Containers vs Virtual Machines
People often confuse containers with VMs. Here's the key difference:

CONTAINERS VS VIRTUAL MACHINES
DOCKER CONTAINERS
📦 App A  |  📦 App B  |  📦 App C
Docker Engine
Host OS (Linux/Mac/Win)
✅ Shares Host OS kernel
✅ Starts in seconds
✅ Uses MBs of RAM
✅ Lightweight & fast
VIRTUAL MACHINES
App A
Guest OS + Libs
App B
Guest OS + Libs
Hypervisor + Host OS
❌ Full OS per VM
❌ Minutes to start
❌ Uses GBs of RAM
❌ Heavy overhead
02
🏗️
Docker Architecture
Docker uses a client-server architecture. The Docker client talks to the Docker daemon (server), which does the heavy lifting of building, running, and managing containers.

DOCKER ARCHITECTURE OVERVIEW
💻
Docker Client
docker run, build...
→
⚙️
Docker Daemon
dockerd
⇄
🗄️
Registry
Docker Hub
CONTAINERS (Running)
📦
Container 1
nginx:latest
📦
Container 2
node:18
📦
Container 3
postgres:15
Client — You type commands here
Daemon — Runs in background, manages everything
Registry — Remote store for images
Containers — Running processes
The key flow is: you write a Dockerfile → build it into an Image → push to Registry → pull anywhere → run as a Container.

03
⚡
Installation
Docker Desktop is the easiest way to install Docker on Windows and macOS. On Linux, you install Docker Engine directly.

🪟 Windows
🍎 macOS
🐧 Linux (Ubuntu)
1
Download Docker Desktop
Go to docker.com/products/docker-desktop and download the Windows installer (.exe).

2
Enable WSL 2
Docker Desktop requires WSL 2 (Windows Subsystem for Linux). The installer will guide you through enabling it.

3
Run Installer & Verify
POWERSHELL
Copy
docker --version
# Output: Docker version 27.x.x, build ...
docker run hello-world
04
🚀
First Steps
Let's run your very first container — a simple web server. This proves Docker is working.

BASH — RUN YOUR FIRST CONTAINER
Copy
# Pull & run nginx web server on port 8080
docker run -d -p 8080:80 --name my-nginx nginx

# -d = detached (runs in background)
# -p 8080:80 = map host port 8080 → container port 80
# --name my-nginx = give it a friendly name
# nginx = the image to use (pulled from Docker Hub)
✅
Open http://localhost:8080 in your browser — you'll see the nginx welcome page. Congratulations, you ran your first container!

BASH — BASIC CONTAINER MANAGEMENT
Copy
# List running containers
docker ps

# Stop the container
docker stop my-nginx

# Start it again
docker start my-nginx

# Remove (must stop first)
docker rm my-nginx

# Run interactively (enter the container's shell)
docker run -it ubuntu bash
05
⌨️
Essential CLI Commands
Here are all the commands you'll use day-to-day, grouped by category.

🐳 CONTAINER COMMANDS
COMMAND	DESCRIPTION	EXAMPLE
docker run	Create & start a container	docker run -d nginx
docker ps	List running containers	docker ps -a (all)
docker stop	Gracefully stop container	docker stop my-app
docker start	Start stopped container	docker start my-app
docker restart	Restart a container	docker restart my-app
docker rm	Remove stopped container	docker rm my-app
docker exec	Run command in container	docker exec -it app bash
docker logs	View container logs	docker logs -f my-app
docker inspect	Detailed container info	docker inspect my-app
docker stats	Live resource usage	docker stats
🖼️ IMAGE COMMANDS
COMMAND	DESCRIPTION	EXAMPLE
docker pull	Download image from registry	docker pull node:18
docker images	List local images	docker images
docker build	Build image from Dockerfile	docker build -t myapp .
docker push	Upload image to registry	docker push user/myapp
docker rmi	Remove local image	docker rmi nginx:latest
docker tag	Tag an image	docker tag app user/app:v1
🧹 SYSTEM COMMANDS
BASH
Copy
# Remove all stopped containers
docker container prune

# Remove unused images
docker image prune

# Remove everything unused (containers, images, networks, cache)
docker system prune -a

# Show disk usage
docker system df
06
📄
Dockerfile
A Dockerfile is a plain text file that contains step-by-step instructions to build a Docker image. Think of it as a recipe for your app's environment.

Dockerfile Instructions Reference
INSTRUCTION	WHAT IT DOES
FROM	Base image to start from (every Dockerfile starts with this)
WORKDIR	Set the working directory inside the container
COPY	Copy files from your machine into the image
ADD	Like COPY but also supports URLs and auto-extracts archives
RUN	Execute a command during image build (install packages etc.)
ENV	Set environment variables
EXPOSE	Document which port the app uses (informational)
CMD	Default command to run when container starts (can be overridden)
ENTRYPOINT	Command that always runs (harder to override)
ARG	Build-time variable (not available in the running container)
VOLUME	Create a mount point for persistent data
USER	Set the user to run subsequent commands as
Example: Node.js App
DOCKERFILE
Copy
# Start with official Node.js 18 image (Alpine = tiny Linux)
FROM node:18-alpine

# Set working directory inside container
WORKDIR /app

# Copy package.json first (layer caching optimization)
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy rest of app code
COPY . .

# Set environment variable
ENV NODE_ENV=production

# Document the port
EXPOSE 3000

# Command to start the app
CMD ["node", "index.js"]
BASH — BUILD & RUN
Copy
# Build the image (. = current directory)
docker build -t my-node-app:1.0 .

# Run it
docker run -d -p 3000:3000 my-node-app:1.0
⚠️
Always create a .dockerignore file to exclude files you don't want copied into the image (like node_modules, .git, .env files).

.DOCKERIGNORE
Copy
node_modules
.git
.env
*.log
dist
.DS_Store
Multi-Stage Build (Advanced but useful)
Use multiple FROM stages to keep your final image small — only include what's needed to run, not to build.

DOCKERFILE — MULTI-STAGE
Copy
# Stage 1: Build
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production (tiny image)
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package*.json ./
RUN npm install --production
CMD ["node", "dist/index.js"]
07
🎼
Docker Compose
Most real applications need multiple services — a web server, a database, a cache. Docker Compose lets you define and run all of them together using a single docker-compose.yml file.

TYPICAL APP STACK WITH DOCKER COMPOSE
🌐
web
:3000 → :3000
⇆
🗄️
database
postgres:5432
⇆
⚡
cache
redis:6379
DOCKER-COMPOSE.YML — FULL EXAMPLE
Copy
version: '3.8'

services:

  # ─── Web Application ─────────────────────────
  web:
    build: .                    # Build from local Dockerfile
    ports:
      - "3000:3000"             # HOST:CONTAINER
    environment:
      - DATABASE_URL=postgres://user:pass@db:5432/mydb
      - REDIS_URL=redis://cache:6379
    depends_on:
      - db
      - cache
    volumes:
      - .:/app                   # Mount code for live reload
      - /app/node_modules

  # ─── PostgreSQL Database ──────────────────────
  db:
    image: postgres:15
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data  # Persist data
    ports:
      - "5432:5432"

  # ─── Redis Cache ──────────────────────────────
  cache:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  postgres_data:                # Named volume (persists between runs)
Docker Compose Commands
COMMAND	DESCRIPTION
docker compose up	Start all services (add -d for background)
docker compose down	Stop and remove all containers
docker compose ps	List running services
docker compose logs	View logs from all services
docker compose build	Rebuild images
docker compose exec web bash	Open shell in web service
docker compose restart	Restart all services
docker compose down -v	Stop and also remove volumes
08
🌐
Networking
Docker containers can communicate with each other and the outside world through Docker networks. By default, Docker creates a bridge network for containers.

DOCKER NETWORK TYPES
bridge
Default. Containers talk to each other on same network.
host
Container shares host's network. No isolation.
none
No networking. Fully isolated.
BASH — NETWORK COMMANDS
Copy
# List networks
docker network ls

# Create a custom network
docker network create my-network

# Run container on custom network
docker run -d --name app --network my-network nginx

# Connect existing container to network
docker network connect my-network container-name

# Inspect network (see which containers are connected)
docker network inspect my-network

# Containers on the same network can talk by NAME
# e.g., from the 'web' container you can reach 'db' at http://db:5432
💡
Key rule: Containers on the same Docker network can reach each other using their container name as the hostname. So if you have a container named db, your app can connect to db:5432. No IP addresses needed!

Port Mapping
Port mapping connects a port on your host machine to a port inside the container.

BASH — PORT MAPPING EXAMPLES
Copy
# Format: -p HOST_PORT:CONTAINER_PORT
docker run -p 8080:80 nginx     # Access via localhost:8080
docker run -p 5432:5432 postgres  # Access via localhost:5432

# Bind to specific IP (only allow local access)
docker run -p 127.0.0.1:8080:80 nginx

# Map multiple ports
docker run -p 80:80 -p 443:443 nginx
09
💾
Volumes & Storage
Containers are ephemeral — when you remove a container, its data is gone. Volumes solve this by storing data outside the container, so it persists even when the container is deleted.

HOST MACHINE
/home/user/mydata
Files live here on your real computer
⇆
CONTAINER
/app/data
Container reads/writes here
Three Types of Storage
📂
Named Volumes
Managed by Docker. Best for databases. Data lives in Docker's storage area. Persists between container restarts.

-v mydata:/app/data
🔗
Bind Mounts
Mount a specific folder from your machine. Great for development — changes in your code are instant inside the container.

-v /host/path:/container/path
⚡
tmpfs Mounts
Stored only in host memory. Fastest but not persistent. Good for sensitive temporary data.

--tmpfs /app/temp
BASH — VOLUME COMMANDS
Copy
# Create a named volume
docker volume create my-data

# List volumes
docker volume ls

# Use named volume
docker run -v my-data:/app/data nginx

# Bind mount (your code folder → container)
docker run -v $(pwd):/app node:18

# Inspect a volume
docker volume inspect my-data

# Remove a volume
docker volume rm my-data

# Remove all unused volumes
docker volume prune
⚠️
Important: Never store database data inside a container without a volume. If the container is deleted, all your data disappears. Always use a named volume for databases like PostgreSQL, MySQL, MongoDB.

10
📋
Quick Reference Cheat Sheet
// CONTAINER LIFECYCLE
docker run -d nginx
Start in bg
docker ps -a
All containers
docker stop NAME
Stop container
docker rm NAME
Delete container
docker rm -f NAME
Force delete
// IMAGES
docker pull nginx
Download image
docker images
List images
docker build -t app .
Build image
docker rmi IMAGE
Delete image
docker push user/app
Push to hub
// DEBUGGING
docker logs NAME
View logs
docker logs -f NAME
Follow logs
docker exec -it NAME bash
Open shell
docker inspect NAME
Full details
docker stats
CPU/RAM usage
// DOCKER COMPOSE
docker compose up -d
Start all
docker compose down
Stop all
docker compose logs -f
Follow logs
docker compose ps
List services
docker compose build
Rebuild images
// NETWORKS
docker network ls
List networks
docker network create X
Create network
--network X
Join network
-p 8080:80
Map port
docker network inspect X
View details
// VOLUMES
docker volume ls
List volumes
docker volume create X
Create volume
-v name:/path
Named volume
-v /host:/container
Bind mount
docker volume prune
Clean up
🎯
What's Next? Now that you know the basics, explore: Docker Hub (public image registry), Docker Swarm or Kubernetes (orchestration for production), multi-architecture builds, and CI/CD pipeline integration with Docker.

🐳 Docker Documentation — Beginner's Guide
Generated with love · 2026
