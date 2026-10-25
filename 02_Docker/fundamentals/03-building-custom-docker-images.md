# Building Custom Docker Images

## 📌 What Is a Dockerfile?

A Dockerfile is a text file containing a set of instructions used to build a Docker image.

A Docker image contains everything an application needs to run, such as:

- Application code
- Runtime
- Libraries
- Dependencies
- Configuration

For example, instead of manually installing Java, copying the application JAR file, and configuring the runtime on every server, we can define these steps in a Dockerfile and build a reusable image.

## 🔄 Dockerfile-to-Container Workflow

```text
Dockerfile  ── docker build ──>  Docker Image  ── docker run ──>  Docker Container
```

- **Dockerfile:** Defines the instructions to build an image.
- **Docker Image:** The reusable template containing the application and its requirements.
- **Docker Container:** A runnable instance of an image.

## 📝 Basic Dockerfile Syntax

A Dockerfile typically contains instructions in the following format:
=====================================================================

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

>>>>>>> 4b713e3b1af9e5675511eaa2bedf70c9cb99d24a
>>>>>>>
>>>>>>
>>>>>
>>>>
>>>
>>

```dockerfile
INSTRUCTION argument
```

Example:

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
```

<<<<<<< HEAD
Explanation:

- `FROM` selects the base image.
- `COPY` copies the application file into the image.
- `EXPOSE` documents the port on which the application is expected to listen.

Docker executes build instructions in order, subject to its build and caching rules.

## 📂 Build Context

The **build context** is the set of files and directories Docker can access during an image build.

When you run `docker build`, the final argument specifies the build context.

Example project structure:
==========================

- `FROM` selects the base image.
- `COPY` copies a file into the image.
- `EXPOSE` documents the port on which the application is expected to listen.

Docker processes build instructions in order, subject to its build and caching rules.

## Build Context

The build context is the set of files Docker can access for instructions such as `COPY` and `ADD`. When running `docker build`, the final `.` commonly means the current directory is the build context.

Example project:

>>>>>>> 4b713e3b1af9e5675511eaa2bedf70c9cb99d24a
>>>>>>>
>>>>>>
>>>>>
>>>>
>>>
>>

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

<<<<<<< HEAD
Build the image:
================

Build the image from the project directory:

>>>>>>> 4b713e3b1af9e5675511eaa2bedf70c9cb99d24a
>>>>>>>
>>>>>>
>>>>>
>>>>
>>>
>>

```bash
docker build -t my-app:1.0 .
```

<<<<<<< HEAD
Here:

- `-t my-app:1.0` assigns a name and tag to the image.
- `.` specifies the current directory as the build context.

The `app.txt` file is copied from the build context into `/app/app.txt` inside the image.

### Using COPY .

=======
The `app.txt` file is copied from the build context into `/app/app.txt` in the image.

### Using COPY . .

>>>>>>> 4b713e3b1af9e5675511eaa2bedf70c9cb99d24a
>>>>>>>
>>>>>>
>>>>>
>>>>
>>>
>>

```dockerfile
COPY . .
```

<<<<<<< HEAD
This copies the contents of the build context into the current working directory inside the image.

Use a `.dockerignore` file to exclude unwanted files, such as local dependencies, Git metadata, and environment files, from the build context.

## 🛠️ Basic Image-Building Commands

### 1. Build a Docker Image

```bash
docker build -t my-app:1.0 .
```

Builds an image from the Dockerfile in the current directory.

### 2. List Docker Images

```bash
docker images
```

Displays images available locally.

### 3. Run a Container

```bash
docker run --name my-app-container my-app:1.0
```

Creates and starts a container from the specified image.

### 4. List Containers

```bash
docker ps
```

Displays running containers.

To list all containers, including stopped containers:

```bash
docker ps -a
```

## 🧪 Hands-on Practice

### Objective

Build a custom Nginx image that serves your own HTML page.

### Step 1: Create a Project Directory

```bash
mkdir custom-image-demo
cd custom-image-demo
```

### Step 2: Create an `index.html` File

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My Custom Docker Image</title>
</head>
<body>
    <h1>Hello from My Custom Docker Image!</h1>
    <p>My first custom Nginx image.</p>
</body>
</html>
```

### Step 3: Create a Dockerfile

Create a file named `Dockerfile` without a file extension.

```dockerfile
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
```

Explanation:

- `FROM nginx:alpine` uses the lightweight Alpine-based Nginx image as the base.
- `COPY` replaces the default Nginx welcome page with your HTML file.
- `EXPOSE 80` documents the container's expected HTTP port.

### Step 4: Build the Custom Image

```bash
docker build -t custom-nginx:1.0 .
```

### Step 5: Verify the Image

```bash
docker images
```

Confirm that `custom-nginx` appears with the `1.0` tag.

### Step 6: Run the Container

```bash
docker run -d --name custom-nginx-container -p 8080:80 custom-nginx:1.0
```

Here:

- `-d` runs the container in the background.
- `--name` assigns a name to the container.
- `-p 8080:80` maps host port `8080` to container port `80`.

### Step 7: Test the Application

Open the following URL in your browser:

```text
http://localhost:8080
```

You should see your custom HTML page.

### Step 8: Clean Up

```bash
docker stop custom-nginx-container
docker rm custom-nginx-container
```

## 🎯 Important Points

- A Dockerfile defines how an image is built.
- A Docker image is the output of the build process.
- A Docker container is a runnable instance of an image.
- The build context determines which local files are available to `COPY` and `ADD`.
- The `-t` option assigns a name and tag to an image.
- A `.dockerignore` file helps exclude unnecessary files from the build context.
- `EXPOSE` documents a port; `-p` publishes a container port on the host.

## 🎤 Interview Questions

1. What is a Dockerfile?
2. What is the difference between a Dockerfile, Docker image, and Docker container?
3. What is a build context in Docker?
4. What does the `.` mean in `docker build -t my-app:1.0 .`?
5. What is the purpose of the `-t` option?
6. Why should we use a `.dockerignore` file?
7. What is the difference between `EXPOSE` and `-p`?
8. Can multiple containers be created from the same Docker image?

## 📝 Summary

A Dockerfile defines the instructions required to package an application into a Docker image. The image can then be used to create containers that run the application in isolated environments.

The basic workflow is:

```text
Write Dockerfile → Build Image → Run Container → Test Application
```

## 📚 References

- [Docker Docs — Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Docker Docs — Build context](https://docs.docker.com/build/concepts/context/)
- [Docker Docs — Build an image](https://docs.docker.com/get-started/docker-concepts/building-images/build-tag-and-publish-an-image/)
  =======
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
