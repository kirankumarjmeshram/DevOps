# Docker & Terminologies

Docker is an open-source platform that allows developers to package, distribute, and run applications inside isolated environments called containers.

## Docker Image

A read-only template containing the application code, libraries, dependencies, and configuration needed to run an application. Containers are created from images.

## Docker Container

A runnable instance of a Docker image that executes an application in an isolated environment. A container can be started, stopped, and removed.

## Docker Host

The physical or virtual machine on which Docker runs. It provides CPU, memory, storage, and networking resources for containers.

## Docker Daemon

The background service (`dockerd`) that listens for Docker API requests and manages Docker objects such as images, containers, networks, and volumes.

## Docker Client

The command-line tool or other interface used to send commands (such as `docker run`) to the Docker Daemon through the Docker API.

## Docker Registry

A service that stores and distributes Docker images. Examples include Docker Hub, Amazon Elastic Container Registry (ECR), and Azure Container Registry.

## Docker Repository

A collection of related versions of an image, usually identified by tags. For example, `username/my-app:1.0` refers to the `my-app` image with the `1.0` tag.

## Docker Tag

A label used to identify a particular image version, such as `1.0`, `2.0`, or `latest`. The `latest` tag does not necessarily mean the newest image.

## Dockerfile

A text file containing instructions used to build a Docker image. Common instructions include `FROM`, `WORKDIR`, `COPY`, `RUN`, and `CMD`.

## Docker Volume

A storage mechanism used to persist data generated or used by containers. Volume data can remain available even after a container is removed.

## Docker Network

A mechanism that enables containers to communicate with one another and with other systems.

## Docker Architecture

Docker uses a client-server architecture. The Docker Client sends requests to the Docker Daemon through the Docker REST API. The daemon manages Docker objects on the Docker Host and may communicate with a Docker Registry to pull or push images.

### Main Components

1. **Docker Client:** Sends commands such as `docker build`, `docker pull`, and `docker run`.
2. **Docker Daemon:** Builds images and creates, runs, and manages containers, networks, and volumes.
3. **Docker Host:** The machine that runs Docker and provides system resources.
4. **Docker Registry:** Stores and distributes images so they can be shared between environments.

### How Docker Works

1. Write a `Dockerfile` containing the instructions to package an application.
2. Run `docker build` to build an image.
3. Optionally use `docker push` to upload the image to a registry.
4. Use `docker pull` to download an image from a registry.
5. Run `docker run` to create and start a container from the image.
6. The Docker Daemon manages the container while the application executes inside it.

### Example Commands

```bash
# Download the Nginx image
docker pull nginx

# List local images
docker images

# Create and start a container
docker run -d --name my-nginx -p 8080:80 nginx

# List running containers
docker ps

# Stop the container
docker stop my-nginx

# Remove the stopped container
docker rm my-nginx
```

Open `http://localhost:8080` in a browser to access Nginx when the container is running.

## Important Differences

| Component | Purpose |
| --- | --- |
| Docker Image | Template used to create containers |
| Docker Container | Runnable instance of an image |
| Dockerfile | Instructions used to build an image |
| Docker Registry | Stores and distributes images |
| Docker Repository | Holds related image versions |
| Docker Daemon | Manages Docker resources |
| Docker Client | Sends requests to Docker |
| Docker Host | Machine where Docker runs |
| Docker Volume | Persists data |
| Docker Network | Enables container communication |

**Key point:** An image is the template, a container is the runnable instance, the registry stores images, the client sends commands, and the daemon manages Docker resources on the host.
