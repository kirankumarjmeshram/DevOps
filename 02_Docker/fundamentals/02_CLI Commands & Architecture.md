
# Docker Engine Reference Cheat Sheets

**CLI Commands & Architecture Fundamentals**

### 1. Core Paradigm

Docker standardizes application delivery by packaging software and dependencies into isolated container instances running on a shared Linux OS kernel.

**2. Container Lifecycle Architecture**

Registry (Docker Hub)
              |
              | docker pull
              v
          Image  <----- docker build (via Dockerfile)
(Read-Only Layer)
              |
              | docker run
             v
Container
(Writable Layer)

### 3. Container Management Operations

| Command                                     | Description & Purpose                                                               |
| ------------------------------------------- | ----------------------------------------------------------------------------------- |
| `docker run -d --name web -p 80:80 nginx` | Creates and starts a container in detached mode with port mapping (Host:Container). |
| `docker ps`                               | Lists**active** running containers with IDs, names, status, and ports.        |
| `docker ps -a`                            | Lists all containers,**including exited and stopped instances.**              |
| `docker stop <container>`                 | Stops a container gracefully.                                                       |
| `docker start <container>`                | Launches an existing stopped container instance without rebuilding.                 |
| `docker restart <container>`              | Restarts a container.                                                               |
| `docker rm <container>`                   | Removes a stopped container from local storage.                                     |
| `docker rm -f <container>`                | Forces removal of a running container.                                              |

### 4. Image Building & Registry Operations

| Command                              | Description & Purpose                                                            |
| ------------------------------------ | -------------------------------------------------------------------------------- |
| `docker build -t app:v1 .`         | Builds an image from a Dockerfile in the current context with the tag`app:v1`. |
| `docker images`                    | Displays locally cached images along with their sizes, tags, and IDs.            |
| `docker pull <image>:<tag>`        | Downloads the specified image from Docker Hub or another registry.               |
| `docker tag <image> repo/name:tag` | Re-tags an existing image for publishing to a remote repository.                 |
| `docker push repo/name:tag`        | Uploads local image layers to a configured container registry.                   |
| `docker rmi <image_id>`            | Deletes a specified image from host storage if it is not in use.                 |

### 5. Diagnostics, Inspection & Runtime

#### Interactive Shell Access

Execute commands inside active containers for debugging.

| Command                              | Description & Purpose                                                               |
| ------------------------------------ | ----------------------------------------------------------------------------------- |
| `docker exec -it <container> bash` | Opens an interactive Bash shell inside a running container.                         |
| `docker exec -it <container> sh`   | Provides an alternative when Bash is unavailable in minimal images, such as Alpine. |

#### Log Streaming

| Command                                   | Description & Purpose                                                                                          |
| ----------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `docker logs -f --tail 100 <container>` | Displays the latest 100 log lines and follows real-time output to monitor startup issues and application logs. |

### 6. Resource Inspection & System Maintenance

| Command                              | Description & Purpose                                                                                 |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------- |
| `docker inspect <container>`       | Returns detailed JSON information, including IP addresses, mounts, environment variables, and status. |
| `docker stats`                     | Displays live container CPU, memory, and network usage.                                               |
| `docker system prune`              | Removes stopped containers, dangling images, and unused networks, along with eligible build cache.    |
| `docker system prune -a --volumes` | Performs deep cleanup, including unused images and eligible unused volumes.                           |

### 7. Best Practice: Storage Cleanup

Run `docker system prune` periodically in development environments to free disk space consumed by unused containers, dangling image layers, and build cache.

Note: Review cleanup commands carefully before using them, especially commands that include `--volumes`, because removing volumes can delete stored data.
