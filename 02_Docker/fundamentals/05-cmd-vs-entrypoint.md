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
=======

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

>>>>>>> 4b713e3b1af9e5675511eaa2bedf70c9cb99d24a
>>>>>>>
>>>>>>
>>>>>
>>>>
>>>
>>

```text
Hello from Docker
```

<<<<<<< HEAD

### Override CMD

You can replace the default command by providing another command after the image name.
======================================================================================

The default command can be overridden by providing a command after the image name:

>>>>>>> 4b713e3b1af9e5675511eaa2bedf70c9cb99d24a
>>>>>>>
>>>>>>
>>>>>
>>>>
>>>
>>

```bash
docker run --rm hello-app echo "Hello from DevOps"
```

<<<<<<< HEAD
**Output:**
=======

Output:

>>>>>>> 4b713e3b1af9e5675511eaa2bedf70c9cb99d24a
>>>>>>>
>>>>>>
>>>>>
>>>>
>>>
>>

```text
Hello from DevOps
```

<<<<<<< HEAD

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

=======
Only the last `CMD` instruction in a build stage takes effect.

## ENTRYPOINT

`ENTRYPOINT` defines the main executable that the container runs.

Example:

```dockerfile
FROM ubuntu:24.04
>>>>>>> 4b713e3b1af9e5675511eaa2bedf70c9cb99d24a
ENTRYPOINT ["echo"]
CMD ["test"]
```

<<<<<<< HEAD

### Build the Image

```bash
docker build -t entrypoint-demo .
```

### Run the Container

```bash
docker run --rm entrypoint-demo "Hello from Docker"
```

**Output:**
=======

Build and run:

```bash
docker build -t entrypoint-demo .
docker run --rm entrypoint-demo "Hello from Docker"
```

Output:

>>>>>>> 4b713e3b1af9e5675511eaa2bedf70c9cb99d24a
>>>>>>>
>>>>>>
>>>>>
>>>>
>>>
>>

```text
Hello from Docker
```

<<<<<<< HEAD

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
  =======
  The argument supplied after the image name is passed to the `echo` executable. In this example, `ENTRYPOINT` defines the executable and `CMD` provides its default argument.

## CMD vs ENTRYPOINT: Key Differences

| CMD                                                                         | ENTRYPOINT                                                                             |
| --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| Provides a default command or default arguments                             | Defines the main executable                                                            |
| The default command can be replaced by a command supplied to `docker run` | The executable normally remains the same when arguments are supplied to `docker run` |
| Useful for providing defaults                                               | Useful for making an image behave like a specific application or command-line tool     |

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
