# 05. CMD vs ENTRYPOINT

Both `CMD` and `ENTRYPOINT` define what runs when a container starts, but they serve different purposes.

## CMD

`CMD` specifies the default command or default arguments for a container.

Example:

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

The default command can be overridden by providing a command after the image name:

```bash
docker run --rm hello-app echo "Hello from DevOps"
```

Output:

```text
Hello from DevOps
```

Only the last `CMD` instruction in a build stage takes effect.

## ENTRYPOINT

`ENTRYPOINT` defines the main executable that the container runs.

Example:

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

The argument supplied after the image name is passed to the `echo` executable. In this example, `ENTRYPOINT` defines the executable and `CMD` provides its default argument.

## CMD vs ENTRYPOINT: Key Differences

| CMD | ENTRYPOINT |
|---|---|
| Provides a default command or default arguments | Defines the main executable |
| The default command can be replaced by a command supplied to `docker run` | The executable normally remains the same when arguments are supplied to `docker run` |
| Useful for providing defaults | Useful for making an image behave like a specific application or command-line tool |

## Quick Rule to Remember

- Use `CMD` when you want to provide a default command or default arguments that users can replace.
- Use `ENTRYPOINT` when the container should run a particular executable.
- Using both together lets `ENTRYPOINT` define the executable and `CMD` provide its default arguments.

## Hands-on Practice

1. Build and run the `hello-app` example using only `CMD`.
2. Override its command by adding another command after the image name.
3. Build and run the `entrypoint-demo` example using `ENTRYPOINT ["echo"]` and `CMD ["test"]`.
4. Pass a different argument to the container and observe how the output changes.

## Interview Questions

1. What is the difference between `CMD` and `ENTRYPOINT`?
2. What happens when you provide a command after the image name to `docker run`?
3. How do `CMD` and `ENTRYPOINT` work together?
4. Why might an image use `ENTRYPOINT` for a command-line tool?
