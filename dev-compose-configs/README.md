# Docker Compose Configs

We may need ancillary containerised services up-and-running for development (e.g., databases), or it might be easier to configure a single container (e.g., with volume mounts, environment variables, etc.), in a single file. We can use docker compose for this task.

## Starting

To run the container as a detached background process that a tool like VS Code can connect to remotely:

```text
docker compose -f compose-basic.yml up --detach
```

### Starting a Shell in a Running Container

```text
docker exec -it NAME_OF_CONTAINER zsh
```

## Stopping

This will preserve the state of all containers (if they have been modified it in any way).

```text
docker compose -f compose-basic.yml stop
```

## Deleting

To delete all stopped containers in the system,

```text
docker compose -f compose-basic.yml down
```
