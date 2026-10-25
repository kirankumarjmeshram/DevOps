# CMD vs ENTRYPOINT

## 📌 Introduction

`CMD` and `ENTRYPOINT` are Dockerfile instructions that define what command or executable runs when a container starts.

Both instructions control container startup behavior, but they serve different purposes.

- **CMD:** Provides a default command or default arguments.
- **ENTRYPOINT:** Defines the main executable that the container runs.

## 1. CMD Instruction

`CMD` specifies the default command or default arguments for a container when it starts.

### Example

```dockerfile
FROM ubuntu:24.04

CMD ["echo", "Hello from Docker"]
```

### Build the Image

```bash
docker build -t hello-app .
```

### Run the Container

```bash
docker run --rm hello-app
```

**Output:**

```text
Hello from Docker
```

### Override CMD

You can replace the default command by providing another command after the image name.

```bash
docker run --rm hello-app echo "Hello from DevOps"
```

**Output:**

```text
Hello from DevOps
```

### Important Points

- `CMD` provides a default command or default arguments.
- A command supplied to `docker run` can override the default command.
- Only the last `CMD` instruction in a build stage takes effect.

## 2. ENTRYPOINT Instruction

`ENTRYPOINT` defines the main executable that the container runs.

It is useful when an image should behave like a specific application or command-line tool.

### Example

```dockerfile
FROM ubuntu:24.04

ENTRYPOINT ["echo"]
CMD ["test"]
```

### Build the Image

```bash
docker build -t entrypoint-demo .
```

### Run the Container

```bash
docker run --rm entrypoint-demo "Hello from Docker"
```

**Output:**

```text
Hello from Docker
```

### How It Works

In the example above:

- `ENTRYPOINT ["echo"]` defines the executable.
- `CMD ["test"]` provides the default argument.
- `"Hello from Docker"` supplied to `docker run` replaces the default `CMD` arguments.

The effective command becomes:

```bash
echo "Hello from Docker"
```

### Run Without Providing Arguments

```bash
docker run --rm entrypoint-demo
```

**Output:**

```text
test
```

Because no arguments were supplied at runtime, Docker uses the default argument defined by `CMD`.

## 3. CMD vs ENTRYPOINT

| CMD                                                                         | ENTRYPOINT                                                                             |
| --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| Provides a default command or default arguments                             | Defines the main executable                                                            |
| The default command can be replaced by a command supplied to `docker run` | The executable normally remains the same when arguments are supplied to `docker run` |
| Useful for providing defaults                                               | Useful for making an image behave like a specific application or command-line tool     |
| Can be used independently                                                   | Can be used independently or together with `CMD`                                     |

## 4. Using CMD and ENTRYPOINT Together

When both instructions are used in exec form:

- `ENTRYPOINT` defines the executable.
- `CMD` provides default arguments to that executable.
- Arguments supplied after the image name in `docker run` replace the default `CMD` arguments.

### Example

```dockerfile
FROM ubuntu:24.04

ENTRYPOINT ["echo"]

CMD ["Hello from CMD"]
```

Build the image:

```bash
docker build -t greeting-app .
```

Run with the default arguments:

```bash
docker run --rm greeting-app
```

**Output:**

```text
Hello from CMD
```

Override the default arguments:

```bash
docker run --rm greeting-app "Hello from ENTRYPOINT"
```

**Output:**

```text
Hello from ENTRYPOINT
```

In both cases, the `echo` executable defined by `ENTRYPOINT` runs.

## 5. Common Use Cases

### Use CMD

Use `CMD` when you want to provide a default command that users can replace.

Example:

```dockerfile
FROM ubuntu:24.04

CMD ["echo", "Default command"]
```

### Use ENTRYPOINT

Use `ENTRYPOINT` when the container should run a specific executable.

Example:

```dockerfile
FROM ubuntu:24.04

ENTRYPOINT ["echo"]
```

### Use Both

Use both when you want a fixed executable with configurable default arguments.

Example:

```dockerfile
FROM ubuntu:24.04

ENTRYPOINT ["echo"]

CMD ["Hello from Docker"]
```

## 6. Important Notes

- `CMD` and `ENTRYPOINT` define container startup behavior; they do not execute during image building.
- A command supplied after the image name in `docker run` replaces the default command in the standard `CMD` example.
- With an exec-form `ENTRYPOINT`, arguments supplied at runtime are passed to the entrypoint executable and replace the default `CMD` arguments.
- When both instructions are used, `ENTRYPOINT` defines the executable and `CMD` supplies its default arguments.
- Prefer exec form, using JSON-array syntax, for predictable signal handling and process execution.

## 🧪 Hands-on Practice

1. Create a Dockerfile using `CMD ["echo", "Hello from Docker"]`.
2. Build an image named `hello-app`.
3. Run the container and observe the output.
4. Override the default command using `docker run`.
5. Create another Dockerfile using `ENTRYPOINT ["echo"]` and `CMD ["test"]`.
6. Run the container with and without runtime arguments.
7. Observe how the output changes.

## 🎤 Interview Questions

1. What is the difference between `CMD` and `ENTRYPOINT`?
2. What happens when you provide a command after the image name in `docker run`?
3. Can `CMD` and `ENTRYPOINT` be used together?
4. How does `CMD` provide default arguments to `ENTRYPOINT`?
5. What happens when you run an entrypoint-based container without supplying arguments?
6. Why is exec form commonly recommended for `CMD` and `ENTRYPOINT`?
7. When would you use `ENTRYPOINT` instead of `CMD`?

## 📝 Summary

`CMD` provides a default command or default arguments, whereas `ENTRYPOINT` defines the main executable.

When both are used together, `ENTRYPOINT` defines what runs and `CMD` provides the default arguments.

**Quick rule to remember:**

```text
CMD         → Default command or arguments
ENTRYPOINT  → Main executable
Both        → Main executable + default arguments
```

## 📚 References

- [Docker Docs — Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Docker Docs — Running containers](https://docs.docker.com/engine/containers/run/)
