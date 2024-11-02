# Basic Developer Workstation

This `Dockerfile` defines an Ubuntu Linux image into which all of my favourite developer tools have been installed:

- Go
- Python (via [uv](https://docs.astral.sh/uv/))
- Docker

And a few other useful tools - see the Dockerfile for more info.

It has been configured so that you have the option of mounting to `$HOME/.shh` and `$HOME/workspace` directories, for enabling SSH (e.g., for GitHub connectivity) and a shared code workspace.

## Building

```text
docker build . -t ubuntu-dev:latest
```

## Starting

To run the container as a detached background process that a tool like VS Code can connect to remotely:

```text
docker run -d \
    --name dev-workstation \
    -v ~/.ssh:/home/dev/.ssh \
    -v ~/workspace:/home/dev/workspace \
    ubuntu-dev
```

Alternatively, you can connect directly with a shell using:

```text
docker run -it \
    --name dev-workstation \
    -v ~/.ssh:/home/dev/.ssh \
    -v ~/workspace:/home/dev/workspace \
    ubuntu-dev \
    zsh
```

### Starting a Shell in a Running Container

```text
docker exec -it dev-workstation zsh
```

## Stopping

This will preserve the state of the container if you have modified it in any way.

```text
docker stop dev-workstation
```

Use `docker start` to restart, etc.

## Deleting

To delete a stopped container,

```text
docker container rm dev-workstation
```
