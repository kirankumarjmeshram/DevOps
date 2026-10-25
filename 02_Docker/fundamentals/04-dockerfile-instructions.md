# Dockerfile Instructions

## 📌 Introduction

Dockerfile instructions define how a Docker image is built, configured, and executed.

A Dockerfile contains instructions that specify the base image, install dependencies, copy application files, configure environment variables, and define the container's startup behavior.

**Basic syntax:**

```dockerfile
INSTRUCTION argument
```

## 📚 Common Dockerfile Instructions

| Instruction     | Purpose                                                                                        | Example                                    |
| --------------- | ---------------------------------------------------------------------------------------------- | ------------------------------------------ |
| `FROM`        | Specifies the base image                                                                       | `FROM ubuntu:24.04`                      |
| `RUN`         | Executes commands during image building                                                        | `RUN apt-get update`                     |
| `WORKDIR`     | Sets the working directory                                                                     | `WORKDIR /app`                           |
| `COPY`        | Copies files from the build context into the image                                             | `COPY app.jar .`                         |
| `ADD`         | Copies files and supports additional behavior, such as extracting supported local tar archives | `ADD app.tar.gz /app/`                   |
| `ENV`         | Sets environment variables                                                                     | `ENV APP_PORT=8080`                      |
| `ARG`         | Defines a build-time variable                                                                  | `ARG APP_VERSION=1.0`                    |
| `EXPOSE`      | Documents the expected listening port                                                          | `EXPOSE 8080`                            |
| `CMD`         | Defines the default command or arguments                                                       | `CMD ["java", "-jar", "app.jar"]`        |
| `ENTRYPOINT`  | Defines the main executable                                                                    | `ENTRYPOINT ["java", "-jar", "app.jar"]` |
| `USER`        | Sets the user for subsequent build instructions and runtime processes                          | `USER appuser`                           |
| `VOLUME`      | Declares a mount point for persistent or externally mounted data                               | `VOLUME /data`                           |
| `LABEL`       | Adds metadata to the image                                                                     | `LABEL app="webserver"`                  |
| `HEALTHCHECK` | Defines a command to check container health                                                    | `HEALTHCHECK CMD ...`                    |

## 1. FROM

Specifies the base image on which the new image is built.

```dockerfile
FROM ubuntu:24.04
```

Explanation:

- Docker uses the Ubuntu 24.04 image as the foundation for the new image.
- The base image provides the initial filesystem and required operating-system components.
- Choose a suitable base image depending on the application, such as Nginx, Python, or Java.

## 2. RUN

Executes commands during the image build process. It is commonly used to install software, packages, and dependencies.

```dockerfile
FROM ubuntu:24.04

RUN apt-get update
RUN apt-get install -y curl
```

Explanation:

- `apt-get update` updates package information.
- `apt-get install -y curl` installs curl without prompting for confirmation.
- These commands execute during image building, not every time the container starts.

## 3. WORKDIR

Sets the working directory inside the image.

```dockerfile
FROM ubuntu:24.04

WORKDIR /app

RUN pwd
```

Explanation:

- Docker creates `/app` if it does not exist.
- Subsequent instructions use `/app` as the working directory where applicable.
- Relative paths in instructions such as `RUN`, `COPY`, `CMD`, and `ENTRYPOINT` are interpreted relative to the working directory where applicable.

## 4. COPY

Copies files or directories from the build context into the image.

Example project structure:

```text
project/
├── Dockerfile
└── app.txt
```

Dockerfile:

```dockerfile
FROM ubuntu:24.04

WORKDIR /app

COPY app.txt /app/app.txt
```

Explanation:

- `app.txt` is copied from the build context.
- The file becomes available at `/app/app.txt` inside the image.

### Using COPY .

```dockerfile
COPY . .
```

Copies the contents of the build context into the current working directory inside the image.

Use a `.dockerignore` file to exclude unwanted files from the build context.

## 5. ADD

Copies files into the image and supports additional features, including automatically extracting supported local tar archives.

```dockerfile
FROM ubuntu:24.04

WORKDIR /app

ADD application.tar.gz /app/
```

Explanation:

- Docker extracts the supported local tar archive into `/app/`.
- `ADD` provides additional behavior beyond ordinary file copying.

**Best practice:** Prefer `COPY` for ordinary file copying because its purpose is clearer. Use `ADD` when you specifically need its additional behavior.

## 6. ENV

Defines environment variables that are available to subsequent instructions and the running container.

```dockerfile
FROM ubuntu:24.04

ENV APP_ENV=production
ENV APP_PORT=8080

CMD ["sh", "-c", "echo $APP_ENV"]
```

Output when the container starts:

```text
production
```

### Override an Environment Variable

```bash
docker run --rm -e APP_ENV=testing myapp
```

The `-e` option allows an environment variable to be overridden at runtime.

**Important:** Do not put passwords, API keys, or other secrets directly into Dockerfile `ENV` instructions.

## 7. ARG

Defines a variable primarily used during image building.

```dockerfile
FROM ubuntu:24.04

ARG APP_VERSION=1.0

RUN echo "Building version $APP_VERSION"
```

### Pass a Custom Build Argument

```bash
docker build --build-arg APP_VERSION=2.0 -t myapp:2.0 .
```

Here, `APP_VERSION` receives the value `2.0` during the build.

### ARG vs ENV

| ARG                                             | ENV                                                               |
| ----------------------------------------------- | ----------------------------------------------------------------- |
| Intended primarily for build-time configuration | Sets environment variables available to the running container     |
| Values can be supplied using `--build-arg`    | Values can be overridden at runtime using `-e`                  |
| Not intended for secrets                        | Do not store secrets directly in Dockerfile environment variables |

**Important:** Do not use `ARG` for secrets because build arguments can be exposed through build metadata or image history.

## 8. EXPOSE

Documents the port on which the application is expected to listen.

```dockerfile
FROM nginx:alpine

EXPOSE 80
```

**Important:** `EXPOSE 80` does not publish the port to the host.

To make the service accessible through a host port:

```bash
docker run -d -p 8080:80 nginx:alpine
```

Explanation:

- `8080` is the host port.
- `80` is the container port.
- The Nginx server can be accessed at `http://localhost:8080`.

## 9. CMD

Specifies the default command or default arguments for the container when it starts.

```dockerfile
FROM ubuntu:24.04

CMD ["echo", "Hello from Docker"]
```

Build and run:

```bash
docker build -t hello-app .
docker run --rm hello-app
```

Output:

```text
Hello from Docker
```

### Override CMD

```bash
docker run --rm hello-app echo "Hello from DevOps"
```

Output:

```text
Hello from DevOps
```

The command supplied after the image name replaces the default command in this example.

**Important:** Only the last `CMD` instruction in a build stage takes effect.

## 10. ENTRYPOINT

Defines the main executable that the container runs.

```dockerfile
FROM ubuntu:24.04

ENTRYPOINT ["echo"]
CMD ["test"]
```

Build and run:

```bash
docker build -t entrypoint-demo .
docker run --rm entrypoint-demo "Hello from Docker"
```

Output:

```text
Hello from Docker
```

Explanation:

- `ENTRYPOINT ["echo"]` defines the executable.
- `CMD ["test"]` provides default arguments.
- The argument supplied after the image name is passed to the `echo` executable.

### CMD vs ENTRYPOINT

| CMD                                                                         | ENTRYPOINT                                                                             |
| --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| Provides a default command or default arguments                             | Defines the main executable                                                            |
| The default command can be replaced by a command supplied to `docker run` | The executable normally remains the same when arguments are supplied to `docker run` |
| Useful for providing defaults                                               | Useful for making an image behave like a specific application or command-line tool     |

## 11. USER

Sets the user used for subsequent build instructions and the container's default runtime process.

```dockerfile
FROM ubuntu:24.04

RUN useradd --create-home appuser

WORKDIR /app

USER appuser

CMD ["whoami"]
```

Explanation:

- `useradd --create-home appuser` creates a user named `appuser`.
- `USER appuser` switches subsequent instructions and the default runtime process to that user.
- `whoami` prints the current username.

### Why Is USER Important?

Running applications as a non-root user reduces the potential impact of a compromised application.

**Best practice:** Run applications as non-root users whenever possible.

## 12. VOLUME

Declares a mount point intended for persistent or externally mounted data.

```dockerfile
FROM ubuntu:24.04

VOLUME ["/data"]
```

### Create and Use a Named Volume

```bash
docker volume create app-data

docker run -d --name data-demo \
  --mount source=app-data,target=/data \
  ubuntu:24.04 sleep infinity
```

Explanation:

- `docker volume create app-data` creates a named volume.
- `--mount source=app-data,target=/data` mounts the volume at `/data` inside the container.
- Data written to `/data` is stored in the volume rather than being limited to the container's writable layer.

A volume can remain after a container is deleted. Docker does not automatically reconnect an anonymous volume to a new container.

## 13. LABEL

Adds metadata to a Docker image.

```dockerfile
FROM nginx:alpine

LABEL app="webserver"
LABEL version="1.0"
LABEL team="DevOps"
```

Inspect the image metadata:

```bash
docker image inspect my-nginx
```

Labels can document:

- Application name
- Application version
- Team ownership
- Other useful metadata

## 14. HEALTHCHECK

Defines a command Docker uses to determine whether a container is healthy.

```dockerfile
FROM nginx:alpine

RUN apk add --no-cache curl

EXPOSE 80

HEALTHCHECK --interval=30s --timeout=3s \
  --start-period=10s --retries=3 \
  CMD curl -fsS http://localhost/ || exit 1
```

### HEALTHCHECK Options

| Option                 | Purpose                                                                 |
| ---------------------- | ----------------------------------------------------------------------- |
| `--interval=30s`     | Runs the check every 30 seconds                                         |
| `--timeout=3s`       | Allows up to three seconds for each check                               |
| `--start-period=10s` | Allows startup time before failures count toward the retry limit        |
| `--retries=3`        | Marks the container unhealthy after the configured consecutive failures |

Explanation:

- Exit code `0` indicates success.
- Exit code `1` indicates failure.
- Docker updates the container's health status based on the health-check results.

**Important:** An unhealthy status does not automatically restart a container under ordinary Docker restart policies.

## 🎯 Important Points to Remember

- `FROM` specifies the base image.
- `RUN` executes commands during image building.
- `WORKDIR` sets the working directory.
- `COPY` copies files from the build context into the image.
- `ADD` supports additional behavior, including extracting supported local tar archives.
- `ENV` defines environment variables available to the running container.
- `ARG` provides build-time configuration.
- `EXPOSE` documents the expected listening port but does not publish it.
- `CMD` provides the default command or arguments.
- `ENTRYPOINT` defines the main executable.
- `USER` specifies the user for subsequent build instructions and runtime processes.
- `VOLUME` declares a mount point for persistent or externally mounted data.
- `LABEL` adds metadata to an image.
- `HEALTHCHECK` reports container health status; it is not itself a restart policy.

## 🧪 Hands-on Practice

1. Create a Dockerfile using `ubuntu:24.04` as the base image.
2. Set `/app` as the working directory using `WORKDIR`.
3. Install curl using `RUN`.
4. Define an environment variable using `ENV`.
5. Create a non-root user and configure `USER`.
6. Build the image and inspect it using `docker image inspect`.
7. Create a separate Nginx image that uses `EXPOSE` and `HEALTHCHECK`.
8. Compare the container's running state with its health status.

## 🎤 Interview Questions

1. What is the purpose of the `FROM` instruction?
2. What is the difference between `RUN` and `CMD`?
3. What is the difference between `COPY` and `ADD`?
4. What is the difference between `ARG` and `ENV`?
5. Does `EXPOSE` publish a container port to the host?
6. What is the difference between `CMD` and `ENTRYPOINT`?
7. Why should applications run as non-root users?
8. What is the purpose of the `VOLUME` instruction?
9. What is the purpose of Docker image labels?
10. What does `HEALTHCHECK` do?
11. Does Docker automatically restart a container when it becomes unhealthy?
12. Why should secrets not be stored directly in `ENV` or `ARG` instructions?

## 📝 Summary

Dockerfile instructions define how images are built and how containers are configured to run.

Understanding these instructions helps developers create reusable, maintainable, and secure Docker images.

## 📚 References

- [Docker Docs — Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Docker Docs — Build context](https://docs.docker.com/build/concepts/context/)
- [Docker Docs — Docker image building](https://docs.docker.com/get-started/docker-concepts/building-images/)
