# 03. Building Custom Docker Images

## What Is a Dockerfile?

A Dockerfile is a text file containing instructions used to build a Docker image.

A Docker image packages the items an application needs to run, such as application code, runtime, libraries, dependencies, and configuration.

For example, instead of manually installing Java, copying a Spring Boot JAR, and configuring the runtime on every server, these steps can be defined in a Dockerfile and used to build a reusable image.

## Dockerfile-to-Container Workflow

```text
Dockerfile  --docker build-->  Docker Image  --docker run-->  Docker Container
```

- **Dockerfile:** Defines how the image should be built.
- **Docker Image:** The built template containing the application and its requirements.
- **Docker Container:** A runnable instance created from the image.

## Basic Dockerfile Syntax

A Dockerfile instruction generally follows this format:

```dockerfile
INSTRUCTION argument
```

Example:

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
```

- `FROM` selects the base image.
- `COPY` copies a file into the image.
- `EXPOSE` documents the port on which the application is expected to listen.

Docker processes build instructions in order, subject to its build and caching rules.

## Build Context

The build context is the set of files Docker can access for instructions such as `COPY` and `ADD`. When running `docker build`, the final `.` commonly means the current directory is the build context.

Example project:

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

Build the image from the project directory:

```bash
docker build -t my-app:1.0 .
```

The `app.txt` file is copied from the build context into `/app/app.txt` in the image.

### Using COPY . .

```dockerfile
COPY . .
```

This copies the contents of the build context into the current working directory inside the image. Use a `.dockerignore` file to exclude files that should not be sent as part of the build context or copied into the image.

## Basic Image-Building Commands

```bash
# Build an image from the Dockerfile in the current directory
docker build -t my-app:1.0 .

# List local images
docker images

# Create and start a container from the image
docker run --name my-app-container my-app:1.0

# List containers
docker ps -a
```

## Key Points

- A Dockerfile is the recipe; the image is the result of building that recipe.
- A container is created from an image.
- The build context determines which local files can be used by `COPY` and `ADD`.
- The `-t` option gives the image a name and optionally a tag.
- The final `.` in `docker build -t my-app:1.0 .` specifies the current directory as the build context.

## Hands-on Practice

1. Create a directory named `custom-image-demo`.
2. Create an `index.html` file containing a simple message.
3. Create a Dockerfile using `nginx:alpine` as the base image and copy the HTML file to `/usr/share/nginx/html/index.html`.
4. Build the image with a name and tag.
5. Run a container from the image and verify the page in a browser.

## Interview Questions

1. What is a Dockerfile?
2. What is the difference between a Dockerfile, an image, and a container?
3. What does the final `.` mean in `docker build -t my-app:1.0 .`?
4. What is the build context?
5. Why should a `.dockerignore` file be used?
