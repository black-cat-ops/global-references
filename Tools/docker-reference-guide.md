# Docker Reference Guide
## For System Administrators & Developers

A comprehensive reference covering essential operations and powerful features that even experienced users might not know about.

---

## Table of Contents
1. [Installation & Configuration](#installation--configuration)
2. [Container Management](#container-management)
3. [Image Management](#image-management)
4. [Docker Compose](#docker-compose)
5. [Networking](#networking)
6. [Volume & Storage Management](#volume--storage-management)
7. [Docker Registry & Hub](#docker-registry--hub)
8. [Dockerfile Best Practices](#dockerfile-best-practices)
9. [Monitoring & Logging](#monitoring--logging)
10. [Security & Best Practices](#security--best-practices)
11. [Troubleshooting & Debugging](#troubleshooting--debugging)
12. [Advanced Topics](#advanced-topics)

---

## Installation & Configuration

### Installation

**Ubuntu/Debian:**
```bash
# Remove old versions
sudo apt-get remove docker docker-engine docker.io containerd runc

# Install prerequisites
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg lsb-release

# Add Docker's official GPG key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Set up repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

**RHEL/CentOS:**
```bash
# Remove old versions
sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine

# Install yum-utils
sudo yum install -y yum-utils

# Set up repository
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# Install Docker Engine
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Start Docker
sudo systemctl start docker
sudo systemctl enable docker
```

**macOS:**
```bash
# Download Docker Desktop from:
# https://www.docker.com/products/docker-desktop

# Or using Homebrew
brew install --cask docker
```

**Windows:**
Download Docker Desktop from:
```
https://www.docker.com/products/docker-desktop
```

**Verify Installation:**
```bash
docker --version
docker compose version

# Test Docker
docker run hello-world
```

### Post-Installation Setup

**Add User to Docker Group (Linux):**
```bash
sudo usermod -aG docker $USER
# Log out and back in for changes to take effect

# Verify
docker run hello-world  # Should work without sudo
```

**Configure Docker Daemon:**
```bash
# Edit daemon configuration
sudo nano /etc/docker/daemon.json
```

Example daemon.json:
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  },
  "storage-driver": "overlay2",
  "default-address-pools": [
    {
      "base": "172.17.0.0/16",
      "size": 24
    }
  ]
}
```

**Restart Docker:**
```bash
sudo systemctl restart docker
```

### 🎯 Easter Eggs & Power Tips

**1. Docker Context Management**
```bash
# List contexts
docker context ls

# Create context for remote Docker host
docker context create remote --docker "host=ssh://user@remote-host"

# Switch context
docker context use remote

# Return to default
docker context use default

# Remove context
docker context rm remote
```

**2. Docker CLI Configuration**
```bash
# View Docker system information
docker info

# Show Docker disk usage
docker system df

# Detailed disk usage
docker system df -v

# Check Docker daemon events in real-time
docker events

# Filter events
docker events --filter 'type=container' --filter 'event=start'
```

**3. Docker CLI Plugins**
```bash
# List CLI plugins
docker plugin ls

# Install a plugin
docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions
```

---

## Container Management

### Basic Operations

```bash
# Run a container
docker run nginx

# Run in detached mode
docker run -d nginx

# Run with name
docker run -d --name my-nginx nginx

# Run with port mapping
docker run -d -p 8080:80 nginx

# Run with environment variables
docker run -d -e MY_VAR=value nginx

# Run with volume mount
docker run -d -v /host/path:/container/path nginx

# Run interactively
docker run -it ubuntu /bin/bash

# Run and remove after exit
docker run --rm -it ubuntu /bin/bash
```

### Container Lifecycle

```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Start container
docker start container_name

# Stop container
docker stop container_name

# Restart container
docker restart container_name

# Pause container
docker pause container_name

# Unpause container
docker unpause container_name

# Kill container (force stop)
docker kill container_name

# Remove container
docker rm container_name

# Remove running container (force)
docker rm -f container_name

# Remove all stopped containers
docker container prune
```

### Container Interaction

```bash
# Execute command in running container
docker exec container_name ls /app

# Interactive shell in running container
docker exec -it container_name /bin/bash

# View container logs
docker logs container_name

# Follow logs
docker logs -f container_name

# Show last N lines
docker logs --tail 100 container_name

# Show logs since timestamp
docker logs --since 2024-01-01T00:00:00 container_name

# Copy files to/from container
docker cp local_file.txt container_name:/path/in/container
docker cp container_name:/path/in/container/file.txt ./local_file.txt

# View container details
docker inspect container_name

# View container processes
docker top container_name

# View container stats (live)
docker stats container_name

# View container port mappings
docker port container_name
```

### 🎯 Easter Eggs & Power Tips

**1. Advanced Run Options**
```bash
# Run with resource limits
docker run -d --memory="512m" --cpus="1.5" nginx

# Run with restart policy
docker run -d --restart=unless-stopped nginx
docker run -d --restart=on-failure:5 nginx  # Retry 5 times

# Run with custom hostname
docker run -d --hostname my-container nginx

# Run with DNS settings
docker run -d --dns=8.8.8.8 --dns-search=example.com nginx

# Run with added capabilities
docker run -d --cap-add=NET_ADMIN nginx

# Run in read-only mode
docker run -d --read-only nginx

# Run with custom user
docker run -d --user 1000:1000 nginx

# Run with ulimits
docker run -d --ulimit nofile=1024:2048 nginx

# Run with labels
docker run -d --label env=production --label team=devops nginx
```

**2. Container Inspection Techniques**
```bash
# Get specific field from inspect
docker inspect --format='{{.State.Status}}' container_name

# Get IP address
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name

# Get all environment variables
docker inspect --format='{{range .Config.Env}}{{println .}}{{end}}' container_name

# Get mounted volumes
docker inspect --format='{{range .Mounts}}{{.Source}}:{{.Destination}}{{println}}{{end}}' container_name

# Get container creation time
docker inspect --format='{{.Created}}' container_name

# Export inspect to JSON file
docker inspect container_name > container_info.json
```

**3. Container Filtering & Formatting**
```bash
# List containers with custom format
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"

# Filter containers by status
docker ps -a --filter "status=exited"

# Filter by name
docker ps --filter "name=nginx"

# Filter by label
docker ps --filter "label=env=production"

# Filter by ancestor (image)
docker ps --filter "ancestor=nginx"

# Get only container IDs
docker ps -q

# Get only container names
docker ps --format '{{.Names}}'

# Count running containers
docker ps -q | wc -l
```

**4. Bulk Container Operations**
```bash
# Stop all running containers
docker stop $(docker ps -q)

# Remove all containers
docker rm $(docker ps -a -q)

# Remove all stopped containers
docker container prune -f

# Stop and remove all containers
docker stop $(docker ps -q) && docker rm $(docker ps -a -q)

# Stop containers matching pattern
docker stop $(docker ps --filter "name=test*" -q)

# Remove containers older than 24h
docker container prune --filter "until=24h"
```

**5. Container Updates & Modifications**
```bash
# Update container configuration
docker update --memory="1g" --cpus="2" container_name

# Update restart policy
docker update --restart=always container_name

# Rename container
docker rename old_name new_name

# Create image from running container
docker commit container_name my_new_image:tag

# Create image with change message
docker commit -m "Added configuration" container_name my_image:v2
```

**6. Advanced Logging**
```bash
# Show logs with timestamps
docker logs -f --timestamps container_name

# Show logs since specific time
docker logs --since 1h container_name
docker logs --since 2024-01-01T10:00:00 container_name

# Show logs until specific time
docker logs --until 2024-01-01T12:00:00 container_name

# Combine filters
docker logs --since 1h --until 30m container_name

# Follow logs from multiple containers
docker logs -f container1 & docker logs -f container2

# Export logs to file
docker logs container_name > container.log 2>&1
```

---

## Image Management

### Basic Operations

```bash
# Search Docker Hub for images
docker search nginx

# Pull image from registry
docker pull nginx

# Pull specific tag
docker pull nginx:1.21-alpine

# List local images
docker images

# List all images (including intermediate)
docker images -a

# Remove image
docker rmi nginx

# Remove image by ID
docker rmi 3fa7b4c1a123

# Force remove image
docker rmi -f nginx

# Tag image
docker tag nginx:latest mynginx:v1

# Build image from Dockerfile
docker build -t myapp:v1 .

# Build with no cache
docker build --no-cache -t myapp:v1 .
```

### 🎯 Easter Eggs & Power Tips

**1. Advanced Image Operations**
```bash
# Pull all tags of an image
docker pull -a nginx

# Show image history (layers)
docker history nginx

# Show detailed image history
docker history --no-trunc nginx

# Inspect image
docker inspect nginx

# Get image digest
docker images --digests nginx

# Get image size
docker images --format "{{.Repository}}:{{.Tag}} - {{.Size}}"

# Save image to tar file
docker save nginx -o nginx.tar
docker save nginx | gzip > nginx.tar.gz

# Load image from tar file
docker load -i nginx.tar
docker load < nginx.tar.gz

# Export container filesystem
docker export container_name -o filesystem.tar

# Import filesystem as image
docker import filesystem.tar mynewimage:v1
```

**2. Image Filtering & Search**
```bash
# List images with filter
docker images --filter "dangling=true"

# List images by reference
docker images --filter=reference='nginx:*'

# List images before/after specific image
docker images --filter "before=nginx:1.21"
docker images --filter "since=nginx:1.21"

# List images by label
docker images --filter "label=maintainer=nginx"

# Show only image IDs
docker images -q

# Count images
docker images -q | wc -l

# Custom format output
docker images --format "{{.Repository}}:{{.Tag}} ({{.Size}})"
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"
```

**3. Image Cleanup**
```bash
# Remove dangling images
docker image prune

# Remove all unused images
docker image prune -a

# Remove images older than 24h
docker image prune -a --filter "until=24h"

# Remove images with force
docker image prune -af

# Remove all images
docker rmi $(docker images -q)

# Remove images matching pattern
docker rmi $(docker images --filter=reference='test/*' -q)
```

**4. Multi-platform Builds**
```bash
# Create buildx builder
docker buildx create --name mybuilder --use

# Build for multiple platforms
docker buildx build --platform linux/amd64,linux/arm64 -t myapp:v1 .

# Build and push multi-platform image
docker buildx build --platform linux/amd64,linux/arm64 -t user/myapp:v1 --push .

# Inspect builder
docker buildx inspect

# List builders
docker buildx ls
```

**5. Image Analysis**
```bash
# Analyze image layers with dive (external tool)
# Install: https://github.com/wagoodman/dive
dive nginx

# Show what changed in image layers
docker diff container_name

# Get image manifest
docker manifest inspect nginx

# Get configuration
docker inspect --format='{{.Config}}' nginx
```

---

## Docker Compose

### Installation

Docker Compose V2 comes with Docker Desktop and modern Docker installations.

**Verify Installation:**
```bash
docker compose version
```

### Basic Operations

**Sample docker-compose.yml:**
```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    environment:
      - NGINX_HOST=localhost
      - NGINX_PORT=80
    networks:
      - webnet
    restart: unless-stopped

  db:
    image: postgres:14
    environment:
      POSTGRES_PASSWORD: secretpassword
      POSTGRES_DB: myapp
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - webnet

networks:
  webnet:

volumes:
  db-data:
```

**Basic Commands:**
```bash
# Start services
docker compose up

# Start in detached mode
docker compose up -d

# Start specific services
docker compose up web

# Stop services
docker compose down

# Stop and remove volumes
docker compose down -v

# View running services
docker compose ps

# View logs
docker compose logs

# Follow logs
docker compose logs -f

# View logs for specific service
docker compose logs -f web

# Execute command in service
docker compose exec web sh

# Restart services
docker compose restart

# Pull latest images
docker compose pull

# Build images
docker compose build

# Build without cache
docker compose build --no-cache
```

### 🎯 Easter Eggs & Power Tips

**1. Advanced Compose Operations**
```bash
# Start with specific compose file
docker compose -f docker-compose.prod.yml up -d

# Use multiple compose files (override)
docker compose -f docker-compose.yml -f docker-compose.override.yml up -d

# Scale services
docker compose up -d --scale web=3

# Force recreate containers
docker compose up -d --force-recreate

# Remove orphan containers
docker compose up -d --remove-orphans

# View config (merged compose files)
docker compose config

# Validate compose file
docker compose config --quiet

# List images used by services
docker compose images

# Show port bindings
docker compose port web 80

# Pause services
docker compose pause

# Unpause services
docker compose unpause

# Top (show running processes)
docker compose top
```

**2. Environment & Variables**
```bash
# Use .env file (automatically loaded)
cat .env
DB_PASSWORD=secret123
DB_NAME=myapp

# Reference in compose file
environment:
  POSTGRES_PASSWORD: ${DB_PASSWORD}
  POSTGRES_DB: ${DB_NAME}

# Override with environment variable
DB_PASSWORD=newpass docker compose up -d

# Use env_file in compose
env_file:
  - ./config/common.env
  - ./config/app.env
```

**3. Advanced Compose File Techniques**
```yaml
# Anchors and aliases for reusability
x-common-variables: &common-vars
  ENVIRONMENT: production
  LOG_LEVEL: info

services:
  web:
    environment:
      <<: *common-vars
      SERVICE_NAME: web
  
  api:
    environment:
      <<: *common-vars
      SERVICE_NAME: api

# Extends (reuse configurations)
services:
  web:
    extends:
      file: common-services.yml
      service: webapp

# Depends on with condition
services:
  web:
    depends_on:
      db:
        condition: service_healthy
  
  db:
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

# Resource limits
services:
  web:
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 256M
```

**4. Compose Profiles**
```yaml
services:
  web:
    image: nginx
  
  debug-tools:
    image: nicolaka/netshoot
    profiles: ["debug"]
  
  monitoring:
    image: grafana/grafana
    profiles: ["monitoring", "full"]
```

```bash
# Start with specific profile
docker compose --profile debug up -d

# Start with multiple profiles
docker compose --profile debug --profile monitoring up -d
```

**5. Compose Watch & Development**
```bash
# Watch for changes and rebuild (Compose V2.22+)
docker compose watch

# Define watch in compose file
services:
  web:
    build: .
    develop:
      watch:
        - action: sync
          path: ./src
          target: /app/src
        - action: rebuild
          path: package.json
```

---

## Networking

### Basic Operations

```bash
# List networks
docker network ls

# Create network
docker network create mynetwork

# Create network with subnet
docker network create --subnet=172.18.0.0/16 mynetwork

# Create network with gateway
docker network create --subnet=172.18.0.0/16 --gateway=172.18.0.1 mynetwork

# Inspect network
docker network inspect mynetwork

# Connect container to network
docker network connect mynetwork container_name

# Disconnect container from network
docker network disconnect mynetwork container_name

# Remove network
docker network rm mynetwork

# Remove all unused networks
docker network prune
```

### 🎯 Easter Eggs & Power Tips

**1. Advanced Network Types**
```bash
# Create bridge network (default type)
docker network create --driver bridge mybridge

# Create overlay network (for Swarm)
docker network create --driver overlay --attachable myoverlay

# Create macvlan network
docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=eth0 macvlan-net

# Create host network (container uses host networking)
docker run --network host nginx

# Create none network (no networking)
docker run --network none nginx
```

**2. Network Inspection & Troubleshooting**
```bash
# Show which containers are on network
docker network inspect mynetwork --format '{{range .Containers}}{{.Name}} {{end}}'

# Get network ID
docker network ls --filter name=mynetwork -q

# Get container IP in network
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name

# Get all IPs for container
docker inspect --format='{{json .NetworkSettings.Networks}}' container_name | jq

# Show network connectivity
docker run --rm --network container:target_container nicolaka/netshoot ping another_container

# DNS lookup inside container
docker exec container_name nslookup another_container
```

**3. Network Aliases & Links**
```bash
# Create container with network alias
docker run -d --network mynetwork --network-alias db postgres

# Connect to network with alias
docker network connect --alias web mynetwork container_name

# Legacy linking (deprecated but still works)
docker run -d --name db postgres
docker run -d --link db:database webapp
```

**4. Port Publishing Variations**
```bash
# Publish to specific host IP
docker run -d -p 127.0.0.1:8080:80 nginx

# Publish to random port
docker run -d -p 80 nginx

# Publish multiple ports
docker run -d -p 8080:80 -p 8443:443 nginx

# Publish range of ports
docker run -d -p 8000-8010:8000-8010 nginx

# Publish UDP port
docker run -d -p 53:53/udp nginx

# Publish all exposed ports
docker run -d -P nginx
```

**5. Network Security**
```bash
# Create isolated network
docker network create --internal isolated-net

# Run container in isolated network
docker run -d --network isolated-net alpine

# Create encrypted overlay network
docker network create \
  --driver overlay \
  --opt encrypted \
  secure-overlay
```

---

## Volume & Storage Management

### Basic Operations

```bash
# List volumes
docker volume ls

# Create volume
docker volume create myvolume

# Create volume with driver options
docker volume create --driver local \
  --opt type=none \
  --opt o=bind \
  --opt device=/path/on/host myvolume

# Inspect volume
docker volume inspect myvolume

# Remove volume
docker volume rm myvolume

# Remove all unused volumes
docker volume prune

# Mount volume to container
docker run -d -v myvolume:/data nginx

# Mount bind mount
docker run -d -v /host/path:/container/path nginx

# Read-only mount
docker run -d -v myvolume:/data:ro nginx
```

### 🎯 Easter Eggs & Power Tips

**1. Advanced Volume Operations**
```bash
# Create volume with labels
docker volume create --label env=prod --label team=devops myvolume

# Filter volumes by label
docker volume ls --filter label=env=prod

# Get volume mountpoint
docker volume inspect --format '{{.Mountpoint}}' myvolume

# Backup volume
docker run --rm -v myvolume:/source -v $(pwd):/backup alpine tar czf /backup/backup.tar.gz -C /source .

# Restore volume
docker run --rm -v myvolume:/target -v $(pwd):/backup alpine tar xzf /backup/backup.tar.gz -C /target

# Copy data between volumes
docker run --rm -v source-vol:/from -v target-vol:/to alpine sh -c "cd /from && cp -av . /to"

# Find volumes not used by any container
docker volume ls -qf dangling=true
```

**2. Mount Types & Options**
```bash
# Tmpfs mount (in-memory)
docker run -d --tmpfs /app/tmp:rw,size=64m,mode=1777 nginx

# Named volume with mount syntax
docker run -d --mount source=myvolume,target=/data nginx

# Bind mount with mount syntax
docker run -d --mount type=bind,source=/host/path,target=/container/path nginx

# Read-only bind mount
docker run -d --mount type=bind,source=/host/path,target=/container/path,readonly nginx

# Tmpfs with mount syntax
docker run -d --mount type=tmpfs,target=/app/cache,tmpfs-size=64m nginx

# Volume with specific driver
docker volume create --driver local \
  --opt type=nfs \
  --opt o=addr=192.168.1.1,rw \
  --opt device=:/path/to/dir nfs-volume
```

**3. Volume Permissions & Ownership**
```bash
# Create volume with specific uid/gid
docker volume create --driver local \
  --opt type=none \
  --opt o=bind,uid=1000,gid=1000 \
  --opt device=/path/on/host myvolume

# Change ownership in volume
docker run --rm -v myvolume:/data alpine chown -R 1000:1000 /data

# Set permissions
docker run --rm -v myvolume:/data alpine chmod -R 755 /data
```

**4. Storage Drivers**
```bash
# Check current storage driver
docker info | grep "Storage Driver"

# View storage driver statistics
docker info --format '{{.DriverStatus}}'

# Overlay2 (recommended for most cases)
# AUFS (older)
# Btrfs (for Btrfs filesystems)
# ZFS (for ZFS filesystems)
# DeviceMapper (legacy)
```

**5. Inspecting Container Storage**
```bash
# Show container's filesystem changes
docker diff container_name

# Get container's writable layer size
docker ps -s

# Show detailed size information
docker ps --format "table {{.Names}}\t{{.Size}}"

# Inspect container volumes
docker inspect --format='{{range .Mounts}}{{.Type}}: {{.Source}} -> {{.Destination}}{{println}}{{end}}' container_name
```

---

## Docker Registry & Hub

### Docker Hub Operations

```bash
# Login to Docker Hub
docker login

# Login with username
docker login -u username

# Login to private registry
docker login registry.example.com

# Logout
docker logout

# Tag image for registry
docker tag myapp:v1 username/myapp:v1

# Push to Docker Hub
docker push username/myapp:v1

# Pull from Docker Hub
docker pull username/myapp:v1

# Search Docker Hub
docker search nginx --limit 25
```

### 🎯 Easter Eggs & Power Tips

**1. Private Registry Setup**
```bash
# Run local registry
docker run -d -p 5000:5000 --name registry registry:2

# Run with persistent storage
docker run -d -p 5000:5000 \
  -v registry-data:/var/lib/registry \
  --name registry registry:2

# Tag for local registry
docker tag myapp:v1 localhost:5000/myapp:v1

# Push to local registry
docker push localhost:5000/myapp:v1

# Pull from local registry
docker pull localhost:5000/myapp:v1

# List images in local registry
curl http://localhost:5000/v2/_catalog

# Get tags for specific image
curl http://localhost:5000/v2/myapp/tags/list
```

**2. Registry with Authentication**
```bash
# Create password file
mkdir auth
docker run --rm --entrypoint htpasswd httpd:2 -Bbn testuser testpass > auth/htpasswd

# Run registry with authentication
docker run -d -p 5000:5000 \
  -v $(pwd)/auth:/auth \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e "REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd" \
  registry:2

# Login to authenticated registry
docker login localhost:5000
```

**3. Registry with TLS**
```bash
# Generate self-signed certificate
mkdir certs
openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt

# Run registry with TLS
docker run -d -p 5000:5000 \
  -v $(pwd)/certs:/certs \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  registry:2
```

**4. Image Signing & Verification**
```bash
# Enable Docker Content Trust
export DOCKER_CONTENT_TRUST=1

# Push signed image
docker push username/myapp:v1

# Pull with signature verification
docker pull username/myapp:v1

# Disable content trust
export DOCKER_CONTENT_TRUST=0
```

**5. Registry Maintenance**
```bash
# Garbage collection (in registry container)
docker exec registry bin/registry garbage-collect /etc/docker/registry/config.yml

# Delete image from registry
# Requires registry to run with REGISTRY_STORAGE_DELETE_ENABLED=true
curl -X DELETE http://localhost:5000/v2/myapp/manifests/sha256:digest
```

---

## Dockerfile Best Practices

### Basic Dockerfile

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["node", "server.js"]
```

### 🎯 Easter Eggs & Power Tips

**1. Multi-Stage Builds**
```dockerfile
# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./
EXPOSE 3000
CMD ["node", "dist/server.js"]
```

**2. Optimization Techniques**
```dockerfile
# Use specific tags, not 'latest'
FROM node:18.17-alpine

# Combine RUN commands to reduce layers
RUN apt-get update && \
    apt-get install -y curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Use .dockerignore to exclude files
# .dockerignore content:
# node_modules
# .git
# *.md
# .env

# Leverage build cache - copy dependencies first
COPY package*.json ./
RUN npm ci --only=production
COPY . .

# Use ARG for build-time variables
ARG VERSION=1.0
LABEL version=$VERSION

# Use ENV for runtime variables
ENV NODE_ENV=production \
    PORT=3000
```

**3. Security Best Practices**
```dockerfile
# Run as non-root user
FROM node:18-alpine

# Create app user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

WORKDIR /app

# Install dependencies as root
COPY package*.json ./
RUN npm ci --only=production

# Copy app files
COPY --chown=nodejs:nodejs . .

# Switch to non-root user
USER nodejs

EXPOSE 3000
CMD ["node", "server.js"]
```

**4. Advanced Dockerfile Features**
```dockerfile
# Heredoc syntax (Docker 23.0+)
FROM alpine
RUN <<EOF
apk add --no-cache git
git clone https://github.com/example/repo.git
cd repo
make install
EOF

# Bind mounts for caching
RUN --mount=type=cache,target=/root/.npm \
    npm install

# Secret mounts
RUN --mount=type=secret,id=aws,target=/root/.aws/credentials \
    aws s3 cp s3://bucket/file .

# SSH mounts
RUN --mount=type=ssh \
    git clone git@github.com:private/repo.git

# Multiple platforms
FROM --platform=$BUILDPLATFORM golang:1.21 AS builder
ARG TARGETPLATFORM
ARG BUILDPLATFORM
RUN echo "Building on $BUILDPLATFORM for $TARGETPLATFORM"
```

**5. Healthchecks in Dockerfile**
```dockerfile
# Simple healthcheck
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:3000/health || exit 1

# Custom healthcheck script
COPY healthcheck.sh /usr/local/bin/
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD ["/usr/local/bin/healthcheck.sh"]

# No healthcheck (inherit from base)
HEALTHCHECK NONE
```

**6. BuildKit Features**
```bash
# Enable BuildKit
export DOCKER_BUILDKIT=1

# Or per-build
DOCKER_BUILDKIT=1 docker build -t myapp .

# Use build secrets
docker build --secret id=aws,src=$HOME/.aws/credentials -t myapp .

# Use SSH agent
docker build --ssh default -t myapp .

# Build with cache from registry
docker build --cache-from myapp:latest -t myapp:new .

# Export build cache
docker build --cache-to type=local,dest=/tmp/cache -t myapp .

# Import build cache
docker build --cache-from type=local,src=/tmp/cache -t myapp .
```

---

## Monitoring & Logging

### Basic Monitoring

```bash
# View container stats
docker stats

# Stats for specific container
docker stats container_name

# Stats without streaming
docker stats --no-stream

# Custom format
docker stats --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}"

# View container logs
docker logs container_name

# Follow logs
docker logs -f container_name

# Show timestamps
docker logs -t container_name

# Show last N lines
docker logs --tail 100 container_name
```

### 🎯 Easter Eggs & Power Tips

**1. Advanced Stats & Monitoring**
```bash
# Show all container stats
docker stats $(docker ps --format '{{.Names}}')

# Export stats to JSON
docker stats --no-stream --format json

# Monitor specific metrics
docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemPerc}}\t{{.NetIO}}\t{{.BlockIO}}"

# Continuous monitoring with watch
watch -n 1 'docker stats --no-stream'

# Memory usage sorted
docker stats --no-stream --format "table {{.Name}}\t{{.MemUsage}}" | sort -k 2 -h
```

**2. Logging Drivers**
```bash
# Set logging driver for container
docker run -d --log-driver json-file --log-opt max-size=10m --log-opt max-file=3 nginx

# Available log drivers:
# - json-file (default)
# - syslog
# - journald
# - gelf
# - fluentd
# - awslogs
# - splunk
# - none

# Use syslog
docker run -d --log-driver syslog --log-opt syslog-address=tcp://192.168.1.100:514 nginx

# Use journald
docker run -d --log-driver journald nginx

# View journald logs
journalctl CONTAINER_NAME=container_name

# No logs
docker run -d --log-driver none nginx
```

**3. Advanced Log Analysis**
```bash
# Search logs with grep
docker logs container_name 2>&1 | grep ERROR

# Count log lines
docker logs container_name 2>&1 | wc -l

# Get logs since specific time
docker logs --since 2024-01-01T10:00:00 container_name

# Get logs until specific time
docker logs --until 2024-01-01T12:00:00 container_name

# Get logs for time range
docker logs --since 1h --until 30m container_name

# Export logs to file
docker logs container_name > /tmp/container.log 2>&1

# Follow logs from multiple containers
docker logs -f container1 & docker logs -f container2

# Real-time log filtering
docker logs -f container_name | grep --line-buffered ERROR
```

**4. Container Events**
```bash
# Watch all Docker events
docker events

# Filter events by type
docker events --filter 'type=container'

# Filter by event
docker events --filter 'event=start'

# Filter by container
docker events --filter 'container=mycontainer'

# Multiple filters
docker events --filter 'type=container' --filter 'event=start'

# Format events
docker events --format '{{.Time}} - {{.Type}} - {{.Action}} - {{.Actor.Attributes.name}}'

# Events since timestamp
docker events --since '2024-01-01T00:00:00'

# Events until timestamp
docker events --until '2024-01-01T12:00:00'
```

**5. Health Checks**
```bash
# Add healthcheck to running container (not directly possible, but can update)
# Healthcheck in run command:
docker run -d --name web \
  --health-cmd='curl -f http://localhost/ || exit 1' \
  --health-interval=30s \
  --health-timeout=3s \
  --health-retries=3 \
  --health-start-period=10s \
  nginx

# Check container health
docker inspect --format='{{.State.Health.Status}}' container_name

# Get health check logs
docker inspect --format='{{range .State.Health.Log}}{{.Output}}{{end}}' container_name

# Filter healthy/unhealthy containers
docker ps --filter health=healthy
docker ps --filter health=unhealthy
```

**6. System-wide Monitoring**
```bash
# Show system-wide information
docker system info

# Show disk usage
docker system df

# Detailed disk usage
docker system df -v

# Monitor system events
docker system events

# System prune (cleanup)
docker system prune -a --volumes
```

---

## Security & Best Practices

### Basic Security

```bash
# Scan image for vulnerabilities
docker scan myimage:latest

# Run container with read-only filesystem
docker run -d --read-only nginx

# Drop all capabilities and add specific ones
docker run -d --cap-drop ALL --cap-add NET_BIND_SERVICE nginx

# Run with security options
docker run -d --security-opt no-new-privileges nginx

# Limit resources
docker run -d --memory="512m" --cpus="1.0" nginx
```

### 🎯 Easter Eggs & Power Tips

**1. Security Scanning**
```bash
# Scan with Docker Scout (built-in)
docker scout quickview myimage:latest

# Detailed CVE information
docker scout cves myimage:latest

# Compare images
docker scout compare --to myimage:v1 myimage:v2

# Scan local image
docker scout cves fs://path/to/dockerfile

# Get recommendations
docker scout recommendations myimage:latest
```

**2. User Namespaces**
```bash
# Enable user namespace remapping
# Edit /etc/docker/daemon.json
{
  "userns-remap": "default"
}

# Restart Docker
sudo systemctl restart docker

# Or use specific user mapping
{
  "userns-remap": "dockeruser:dockeruser"
}

# Check namespace mapping
docker info | grep -i "userns"
```

**3. Secrets Management**
```bash
# Docker Swarm secrets (requires Swarm mode)
docker swarm init
docker secret create my_secret secret.txt
docker service create --name app --secret my_secret nginx

# Docker Compose secrets
# docker-compose.yml:
services:
  app:
    image: myapp
    secrets:
      - db_password

secrets:
  db_password:
    file: ./secrets/db_password.txt

# Environment file (less secure)
docker run -d --env-file .env nginx

# Build-time secrets (BuildKit)
docker build --secret id=npmrc,src=$HOME/.npmrc -t myapp .
```

**4. Content Trust**
```bash
# Enable Docker Content Trust
export DOCKER_CONTENT_TRUST=1

# Generate trust keys
docker trust key generate mykey

# Sign and push image
docker trust sign myimage:v1

# Inspect signatures
docker trust inspect --pretty myimage:v1

# Revoke trust
docker trust revoke myimage:v1
```

**5. AppArmor & SELinux**
```bash
# Run with AppArmor profile
docker run -d --security-opt apparmor=docker-default nginx

# Run with custom AppArmor profile
docker run -d --security-opt apparmor=my-profile nginx

# SELinux labels
docker run -d --security-opt label=level:s0:c100,c200 nginx

# Disable SELinux for container
docker run -d --security-opt label=disable nginx
```

**6. Security Best Practices Checklist**
```bash
# 1. Use official images
docker pull nginx:1.21-alpine  # Not 'latest'

# 2. Run as non-root
USER nodejs

# 3. Read-only root filesystem
docker run -d --read-only --tmpfs /tmp nginx

# 4. Minimal base images
FROM alpine:3.18  # or 'scratch' for Go apps

# 5. No secrets in images
# Use build secrets or environment variables

# 6. Scan regularly
docker scout cves myimage:latest

# 7. Limit resources
docker run -d --memory="256m" --cpus="0.5" --pids-limit 100 nginx

# 8. Drop capabilities
docker run -d --cap-drop ALL --cap-add NET_BIND_SERVICE nginx

# 9. Use trusted registries only

# 10. Keep Docker updated
docker version
```

---

## Troubleshooting & Debugging

### Basic Troubleshooting

```bash
# Check Docker daemon status
sudo systemctl status docker

# View Docker daemon logs
sudo journalctl -u docker -f

# Inspect container
docker inspect container_name

# View container logs
docker logs container_name

# Execute shell in container
docker exec -it container_name /bin/sh

# Check container processes
docker top container_name

# View container resource usage
docker stats container_name

# Check network connectivity
docker exec container_name ping google.com
```

### 🎯 Easter Eggs & Power Tips

**1. Debug Containers**
```bash
# Run debug container in same namespace
docker run -it --rm \
  --network container:target_container \
  --pid container:target_container \
  nicolaka/netshoot

# Run with host networking for debugging
docker run -it --rm --network host nicolaka/netshoot

# Debug with privileged access
docker run -it --rm --privileged --pid=host nicolaka/netshoot

# Enter container's network namespace from host
nsenter -t $(docker inspect -f '{{.State.Pid}}' container_name) -n

# Use docker debug (Docker Desktop)
docker debug container_name
```

**2. Container Inspection Techniques**
```bash
# Get container PID
docker inspect --format '{{.State.Pid}}' container_name

# Get container IP
docker inspect --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name

# Get exit code
docker inspect --format '{{.State.ExitCode}}' container_name

# Get started time
docker inspect --format '{{.State.StartedAt}}' container_name

# Check if container is running
docker inspect --format '{{.State.Running}}' container_name

# Get all environment variables
docker inspect --format='{{range .Config.Env}}{{println .}}{{end}}' container_name

# Export full inspect to JSON
docker inspect container_name | jq '.' > container_inspect.json
```

**3. Network Debugging**
```bash
# Test DNS resolution
docker exec container_name nslookup google.com

# Check connectivity to another container
docker exec container1 ping container2

# View network interfaces
docker exec container_name ip addr

# View routing table
docker exec container_name ip route

# Test port connectivity
docker exec container_name nc -zv hostname 80

# Capture packets (tcpdump in debug container)
docker run -it --rm --net=container:target --cap-add NET_ADMIN nicolaka/netshoot tcpdump -i any

# View open ports
docker exec container_name netstat -tlnp
```

**4. Volume & Storage Debugging**
```bash
# Check volume contents
docker run --rm -v myvolume:/data alpine ls -la /data

# Check volume permissions
docker run --rm -v myvolume:/data alpine stat /data

# Find volume mountpoint on host
docker volume inspect --format '{{.Mountpoint}}' myvolume

# Check container's writable layer size
docker ps -s

# Examine filesystem changes
docker diff container_name

# Export container filesystem
docker export container_name -o filesystem.tar
```

**5. Performance Debugging**
```bash
# Check CPU usage
docker stats --no-stream container_name | grep container_name

# Check memory limits
docker inspect --format '{{.HostConfig.Memory}}' container_name

# Check if container hit memory limit
docker inspect --format '{{.State.OOMKilled}}' container_name

# View container resource limits
docker inspect --format '{{json .HostConfig.Resources}}' container_name | jq

# Trace system calls (requires privileged)
docker run --rm --privileged --pid=container:target alpine strace -p 1

# Profile running container
docker stats --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemPerc}}\t{{.PIDs}}"
```

**6. Log Investigation**
```bash
# Check log driver
docker inspect --format '{{.HostConfig.LogConfig.Type}}' container_name

# Find log file location
docker inspect --format '{{.LogPath}}' container_name

# View raw log file
sudo cat $(docker inspect --format '{{.LogPath}}' container_name)

# Check log size
du -h $(docker inspect --format '{{.LogPath}}' container_name)

# Search logs for patterns
docker logs container_name 2>&1 | grep -i error

# Get timestamp of first log entry
docker logs container_name --timestamps | head -1

# Get timestamp of last log entry
docker logs container_name --timestamps | tail -1
```

**7. Build Debugging**
```bash
# Build with progress output
docker build --progress=plain -t myapp .

# Debug specific build stage
docker build --target builder -t myapp-debug .

# Build with build arguments
docker build --build-arg DEBUG=true -t myapp .

# No cache build
docker build --no-cache -t myapp .

# Check build history
docker history myapp

# Use dive to analyze layers
dive myapp:latest

# Debug with intermediate containers
docker build --rm=false -t myapp .
```

---

## Advanced Topics

### Docker Swarm (Orchestration)

```bash
# Initialize swarm
docker swarm init

# Join swarm as worker
docker swarm join --token TOKEN manager-ip:2377

# Join swarm as manager
docker swarm join-token manager

# List nodes
docker node ls

# Deploy service
docker service create --name web --replicas 3 -p 8080:80 nginx

# List services
docker service ls

# Scale service
docker service scale web=5

# Update service
docker service update --image nginx:1.21 web

# Remove service
docker service rm web

# Leave swarm
docker swarm leave
```

### Docker BuildKit

```bash
# Enable BuildKit
export DOCKER_BUILDKIT=1

# Use inline cache
docker build --build-arg BUILDKIT_INLINE_CACHE=1 -t myapp .

# Use registry cache
docker build --cache-from registry.example.com/myapp:latest -t myapp .

# Export cache
docker build --cache-to type=local,dest=/tmp/cache -t myapp .

# Import cache
docker build --cache-from type=local,src=/tmp/cache -t myapp .

# Multi-platform build
docker buildx build --platform linux/amd64,linux/arm64 -t myapp .
```

### 🎯 Easter Eggs & Power Tips

**1. Advanced BuildKit Features**
```bash
# Create custom builder
docker buildx create --name mybuilder --use

# Build and push in one command
docker buildx build --platform linux/amd64,linux/arm64 -t user/myapp --push .

# Build with output to local filesystem
docker buildx build --output type=local,dest=./out .

# Build with output to tar
docker buildx build --output type=tar,dest=image.tar .

# Use remote builder
docker buildx create --name remote --driver docker-container --driver-opt network=host tcp://remote-host:2375

# Inspect build cache
docker buildx du

# Prune build cache
docker buildx prune
```

**2. Docker Contexts for Remote Hosts**
```bash
# Create SSH context
docker context create remote-ssh --docker "host=ssh://user@remote-host"

# Create TCP context
docker context create remote-tcp --docker "host=tcp://remote-host:2375"

# List contexts
docker context ls

# Use context
docker context use remote-ssh

# Run command on remote
docker --context remote-ssh ps

# Return to default
docker context use default
```

**3. Image Optimization**
```bash
# Use dive to analyze image
dive myapp:latest

# Optimize layer caching
# Bad:
COPY . .
RUN npm install

# Good:
COPY package*.json ./
RUN npm install
COPY . .

# Use multi-stage builds
FROM node:18 AS builder
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html

# Use .dockerignore
node_modules
.git
*.md
.env
```

**4. Container Orchestration Patterns**
```bash
# Sidecar pattern (shared network namespace)
docker run -d --name app myapp
docker run -d --name logger --network container:app logger-image

# Init container pattern (docker-compose)
services:
  app:
    image: myapp
    depends_on:
      init:
        condition: service_completed_successfully
  init:
    image: init-image
    command: /init.sh

# Ambassador pattern
services:
  app:
    image: myapp
  ambassador:
    image: ambassador
    links:
      - app
```

**5. Advanced Networking**
```bash
# Enable IPv6
# Edit /etc/docker/daemon.json
{
  "ipv6": true,
  "fixed-cidr-v6": "2001:db8:1::/64"
}

# Create network with custom MTU
docker network create --opt com.docker.network.driver.mtu=1400 mtunet

# Create network with custom IP range
docker network create --subnet=192.168.100.0/24 --ip-range=192.168.100.128/25 mynet

# Attach container to multiple networks
docker network create net1
docker network create net2
docker run -d --name app --network net1 nginx
docker network connect net2 app
```

**6. Container Resource Management**
```bash
# Set CPU shares (relative weight)
docker run -d --cpu-shares 512 nginx

# Set CPU quota (hard limit)
docker run -d --cpu-quota 50000 --cpu-period 100000 nginx  # 50% of one CPU

# Set CPUs (decimal)
docker run -d --cpus 1.5 nginx

# Set CPU affinity
docker run -d --cpuset-cpus 0,1 nginx

# Set memory reservation (soft limit)
docker run -d --memory-reservation 256m --memory 512m nginx

# Set swap limit
docker run -d --memory 512m --memory-swap 1g nginx  # 512m swap

# Disable swap
docker run -d --memory 512m --memory-swap 512m nginx

# Set kernel memory
docker run -d --kernel-memory 50m nginx

# Limit PIDs
docker run -d --pids-limit 100 nginx

# Block I/O weight
docker run -d --blkio-weight 500 nginx

# Device read/write limits
docker run -d --device-read-bps /dev/sda:1mb nginx
docker run -d --device-write-bps /dev/sda:1mb nginx
```

**7. Checkpoint & Restore (Experimental)**
```bash
# Enable experimental features
# Edit /etc/docker/daemon.json
{
  "experimental": true
}

# Checkpoint container
docker checkpoint create container_name checkpoint1

# List checkpoints
docker checkpoint ls container_name

# Restore from checkpoint
docker start --checkpoint checkpoint1 container_name

# Remove checkpoint
docker checkpoint rm container_name checkpoint1
```

---

## Useful Aliases & Functions

Add these to your `~/.bashrc` or `~/.zshrc`:

```bash
# Basic aliases
alias d='docker'
alias dc='docker compose'
alias dps='docker ps'
alias dpsa='docker ps -a'
alias di='docker images'
alias dex='docker exec -it'
alias dl='docker logs'
alias dlf='docker logs -f'
alias drm='docker rm'
alias drmi='docker rmi'
alias dsp='docker system prune -af'

# Docker compose aliases
alias dcu='docker compose up -d'
alias dcd='docker compose down'
alias dcl='docker compose logs -f'
alias dcr='docker compose restart'
alias dcps='docker compose ps'

# Useful functions
function dsh() {
  docker exec -it "$1" /bin/sh
}

function dbash() {
  docker exec -it "$1" /bin/bash
}

function dip() {
  docker inspect --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' "$1"
}

function dstop-all() {
  docker stop $(docker ps -q)
}

function drm-all() {
  docker rm $(docker ps -a -q)
}

function drmi-all() {
  docker rmi $(docker images -q)
}

function dclean() {
  docker system prune -af --volumes
}

function dlast() {
  docker ps -l -q
}

function drun() {
  docker run -it --rm "$@"
}

function dkill-all() {
  docker kill $(docker ps -q)
}

# Get container stats
function dstats() {
  docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}\t{{.BlockIO}}"
}

# Docker compose functions
function dcup() {
  docker compose up -d "$@" && docker compose logs -f "$@"
}

function dcrestart() {
  docker compose restart "$@" && docker compose logs -f "$@"
}

# Build and run
function dbuild() {
  docker build -t "$1" . && docker run -it --rm "$1"
}

# Quick debug container
function ddebug() {
  docker run -it --rm --network host nicolaka/netshoot
}

# Container shell (auto-detect sh or bash)
function dsh-auto() {
  docker exec -it "$1" sh -c 'if command -v bash >/dev/null; then bash; else sh; fi'
}
```

---

## Quick Reference Tables

### Common Docker Commands
| Command | Description |
|---------|-------------|
| `docker run` | Create and start container |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker stop` | Stop container |
| `docker start` | Start stopped container |
| `docker restart` | Restart container |
| `docker rm` | Remove container |
| `docker images` | List images |
| `docker rmi` | Remove image |
| `docker pull` | Pull image from registry |
| `docker push` | Push image to registry |
| `docker build` | Build image from Dockerfile |
| `docker exec` | Execute command in container |
| `docker logs` | View container logs |
| `docker inspect` | View detailed info |

### Container States
| State | Description |
|-------|-------------|
| created | Container created but not started |
| running | Container is running |
| paused | Container is paused |
| restarting | Container is restarting |
| exited | Container has stopped |
| dead | Container is dead (error state) |

### Restart Policies
| Policy | Description |
|--------|-------------|
| no | Don't restart (default) |
| always | Always restart |
| unless-stopped | Restart unless manually stopped |
| on-failure[:max-retries] | Restart on failure |

### Network Drivers
| Driver | Use Case |
|--------|----------|
| bridge | Default, isolated network |
| host | Use host network stack |
| overlay | Multi-host networking (Swarm) |
| macvlan | Assign MAC address to container |
| none | Disable networking |

### Volume Types
| Type | Description |
|------|-------------|
| Named Volume | Managed by Docker |
| Bind Mount | Mount host directory |
| tmpfs | In-memory temporary filesystem |

---

## Common Pitfalls & Solutions

### Resource Management
**Problem:** Container consuming too much memory
```bash
# Solution: Set memory limits
docker run -d --memory="512m" --memory-swap="512m" nginx
```

**Problem:** Container using all CPU
```bash
# Solution: Limit CPU usage
docker run -d --cpus="1.0" nginx
```

### Networking
**Problem:** Can't connect to container
```bash
# Check if port is exposed
docker port container_name

# Check container IP
docker inspect --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name

# Test connectivity
docker exec container_name ping -c 1 google.com
```

**Problem:** DNS not working
```bash
# Check DNS configuration
docker exec container_name cat /etc/resolv.conf

# Set custom DNS
docker run -d --dns=8.8.8.8 nginx
```

### Storage
**Problem:** Running out of disk space
```bash
# Check disk usage
docker system df

# Clean up
docker system prune -a --volumes

# Remove specific items
docker volume prune
docker image prune -a
```

**Problem:** Volume permissions
```bash
# Fix ownership
docker run --rm -v myvolume:/data alpine chown -R 1000:1000 /data
```

### Images
**Problem:** Image build fails
```bash
# Build with no cache
docker build --no-cache -t myapp .

# Check Dockerfile syntax
docker build --check -t myapp .

# Debug specific stage
docker build --target stagename -t myapp .
```

**Problem:** Large image size
```bash
# Use dive to analyze
dive myapp:latest

# Use multi-stage builds
# Use alpine base images
# Remove unnecessary files in same RUN layer
```

---

## Best Practices Checklist

### Security
- [ ] Run containers as non-root user
- [ ] Use official images from trusted sources
- [ ] Scan images for vulnerabilities regularly
- [ ] Don't include secrets in images
- [ ] Use read-only filesystem when possible
- [ ] Implement resource limits
- [ ] Keep Docker and images updated
- [ ] Use specific image tags, not `latest`

### Performance
- [ ] Use multi-stage builds
- [ ] Leverage build cache
- [ ] Use .dockerignore file
- [ ] Minimize number of layers
- [ ] Use alpine or slim base images
- [ ] Clean up in same RUN layer

### Operations
- [ ] Use health checks
- [ ] Implement proper logging
- [ ] Set restart policies
- [ ] Label containers and images
- [ ] Use Docker Compose for multi-container apps
- [ ] Monitor resource usage
- [ ] Regular cleanup of unused resources

### Development
- [ ] Version your images
- [ ] Use bind mounts for development
- [ ] Use volumes for data persistence
- [ ] Document your Dockerfiles
- [ ] Test builds in CI/CD
- [ ] Use consistent naming conventions

---

## Additional Resources

### Official Documentation
- **Docker Docs**: https://docs.docker.com/
- **Dockerfile Reference**: https://docs.docker.com/engine/reference/builder/
- **Docker Compose**: https://docs.docker.com/compose/
- **Docker Hub**: https://hub.docker.com/

### Tools & Utilities
- **dive**: Analyze image layers - https://github.com/wagoodman/dive
- **ctop**: Top-like interface for containers - https://github.com/bcicen/ctop
- **lazydocker**: Terminal UI for Docker - https://github.com/jesseduffield/lazydocker
- **docker-slim**: Minimize image size - https://github.com/docker-slim/docker-slim
- **hadolint**: Dockerfile linter - https://github.com/hadolint/hadolint

### Learning Resources
- **Play with Docker**: https://labs.play-with-docker.com/
- **Docker Curriculum**: https://docker-curriculum.com/
- **Awesome Docker**: https://github.com/veggiemonk/awesome-docker

### Community
- **Docker Forums**: https://forums.docker.com/
- **Docker Slack**: https://dockercommunity.slack.com/
- **Stack Overflow**: https://stackoverflow.com/questions/tagged/docker

---

*This guide is designed to grow with your Docker journey. Keep experimenting, containerize responsibly, and happy Dockering!*
