# 04. Dockerfile Instructions

A Dockerfile uses instructions to define the base image, install dependencies, copy application files, configure the environment, and define how a container should run.

## Common Instructions

| Instruction | Purpose | Example |
|---|---|---|
| `FROM` | Specifies the base image | `FROM ubuntu:24.04` |
| `RUN` | Executes commands while building the image | `RUN apt-get update` |
| `WORKDIR` | Sets the working directory | `WORKDIR /app` |
| `COPY` | Copies files from the build context into the image | `COPY app.jar .` |
| `ADD` | Copies files and supports additional behavior, such as extracting supported local tar archives | `ADD app.tar.gz /app/` |
| `ENV` | Sets environment variables available to later instructions and the running container | `ENV APP_PORT=8080` |
| `ARG` | Defines a build-time variable | `ARG APP_VERSION=1.0` |
| `EXPOSE` | Documents the expected listening port | `EXPOSE 8080` |
| `USER` | Sets the user for subsequent build instructions and runtime processes | `USER appuser` |
| `VOLUME` | Declares a mount point for persistent or externally mounted data | `VOLUME /data` |
| `LABEL` | Adds metadata to the image | `LABEL app="webserver"` |
| `HEALTHCHECK` | Defines a command used to check container health | `HEALTHCHECK CMD ...` |

## 1. FROM

Specifies the base image on which the new image is built.

```dockerfile
FROM ubuntu:24.04
```

Choose an appropriate base image for the application, such as a web server image or a Python or Java runtime image.

## 2. RUN

Executes commands during image building. It is commonly used to install software, packages, and dependencies.

```dockerfile
FROM ubuntu:24.04
RUN apt-get update
RUN apt-get install -y curl
```

These commands run while the image is being built, not each time a container starts.

## 3. WORKDIR

Sets the working directory inside the image. Docker creates the directory if it does not exist.

```dockerfile
FROM ubuntu:24.04
WORKDIR /app
RUN pwd
```

Subsequent relative paths in instructions such as `RUN`, `COPY`, `CMD`, and `ENTRYPOINT` are interpreted relative to the working directory where applicable.

## 4. COPY

Copies files or directories from the build context into the image.

```dockerfile
FROM ubuntu:24.04
WORKDIR /app
COPY app.txt /app/app.txt
```

The source file must be available in the build context.

## 5. ADD

Copies files into the image and supports additional behavior, including extracting supported local tar archives.

```dockerfile
FROM ubuntu:24.04
WORKDIR /app
ADD application.tar.gz /app/
```

Prefer `COPY` for ordinary file copying because its purpose is clearer. Use `ADD` when you need its additional behavior.

## 6. ENV

Defines environment variables available to subsequent instructions and the running container.

```dockerfile
FROM ubuntu:24.04
ENV APP_ENV=production
ENV APP_PORT=8080
CMD ["sh", "-c", "echo $APP_ENV"]
```

Override an environment variable at runtime:

```bash
docker run --rm -e APP_ENV=testing myapp
```

Do not place passwords, API keys, or other secrets directly in Dockerfile `ENV` instructions.

## 7. ARG

Defines a variable primarily used during image building.

```dockerfile
FROM ubuntu:24.04
ARG APP_VERSION=1.0
RUN echo "Building version $APP_VERSION"
```

Pass a custom value during the build:

```bash
docker build --build-arg APP_VERSION=2.0 -t myapp:2.0 .
```

### ARG vs ENV

| `ARG` | `ENV` |
|---|---|
| Intended primarily for build-time configuration | Sets environment variables that are available to the running container |
| Values can be supplied using `--build-arg` | Values can be overridden at runtime using options such as `-e` |
| Do not use for secrets; build arguments can be exposed through build metadata or image history | Do not store secrets directly in Dockerfile environment variables |

## 8. EXPOSE

Documents the port on which the application is expected to listen.

```dockerfile
FROM nginx:alpine
EXPOSE 80
```

`EXPOSE 80` does not publish the port to the host. Publish a port when starting a container:

```bash
docker run -d -p 8080:80 nginx:alpine
```

Here, `8080` is the host port and `80` is the container port. The service can be accessed at `http://localhost:8080`.

## 9. USER

Sets the user used for subsequent build instructions and the container's default runtime process.

```dockerfile
FROM ubuntu:24.04
RUN useradd --create-home appuser
WORKDIR /app
USER appuser
CMD ["whoami"]
```

Running applications as a non-root user reduces the potential impact of a compromised application.

## 10. VOLUME

Declares a mount point intended for persistent or externally mounted data.

```dockerfile
FROM ubuntu:24.04
VOLUME ["/data"]
```

Create and use a named volume:

```bash
docker volume create app-data
docker run -d --name data-demo \
  --mount source=app-data,target=/data \
  ubuntu:24.04 sleep infinity
```

Data written to `/data` is stored in the named volume rather than being limited to the container's writable layer. A volume can remain after a container is deleted; Docker does not automatically reconnect an anonymous volume to a new container.

## 11. LABEL

Adds metadata to an image.

```dockerfile
FROM nginx:alpine
LABEL app="webserver"
LABEL version="1.0"
LABEL team="DevOps"
```

Inspect image metadata:

```bash
docker image inspect my-nginx
```

Labels can document the application name, version, team, and other useful information.

## 12. HEALTHCHECK

Defines a command Docker uses to determine whether a container is healthy.

```dockerfile
FROM nginx:alpine
RUN apk add --no-cache curl
EXPOSE 80
HEALTHCHECK --interval=30s --timeout=3s \
  --start-period=10s --retries=3 \
  CMD curl -fsS http://localhost/ || exit 1
```

- `--interval=30s`: Runs the check every 30 seconds.
- `--timeout=3s`: Allows up to three seconds for each check.
- `--start-period=10s`: Allows startup time before failures count toward the retry limit.
- `--retries=3`: Marks the container unhealthy after the configured consecutive failures.
- Exit code `0` indicates success; exit code `1` indicates failure.

An unhealthy status does not automatically restart a container under ordinary Docker restart policies.

## Important Notes

- `RUN` executes during image building; `CMD` and `ENTRYPOINT` define container startup behavior.
- `COPY` is preferred for normal file copying; `ADD` has additional behavior.
- `EXPOSE` documents a port but does not publish it.
- Use a non-root user where appropriate.
- Use volumes for data that must persist beyond a container's writable layer.
- A health check reports health status; it is not itself a restart policy.

## Interview Questions

1. What is the purpose of `FROM`?
2. What is the difference between `RUN` and `CMD`?
3. What is the difference between `COPY` and `ADD`?
4. What is the difference between `ARG` and `ENV`?
5. Does `EXPOSE` publish a container port to the host?
6. Why is running as a non-root user recommended?
7. Why are Docker volumes used?
8. What does `HEALTHCHECK` do, and does an unhealthy status automatically restart a container?
