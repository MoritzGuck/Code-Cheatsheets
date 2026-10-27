# Docker 

CLI

tool

container

Docker is a platform for developing, shipping, and running applications in isolated environments.

The key components are:

- **Container**: A lightweight, standalone, executable package that includes everything needed to run a piece of software. It is the running instance of an image (+writable layer).
- **Image**: A read-only template with instructions for creating a container, built from layers. Layers are cached and the respective instructions don’t have to be run again.
- **Dockerfile**: A text file containing commands that define how an image should be *built*. The instructions define the layers of the image.
- **Docker Compose**: A tool for orchestrating multi-container applications. The compose-file defines how a container should *run*.
- **Registry**: A repository for Docker images (like Docker Hub).
- **Volume**: A persistent storage space shared between host and container.
- **Network**: Enables containers to communicate with each other and external resources.

Further concepts:

- **Image tags**: Metadata identifiers for image versions, variants, and architectures.
- **Builds**: Pipeline-based image creation process.
- **Deployments**: Declarative updates to applications.

``` mermaid
flowchart TD
    DF["📄 Dockerfile"] -->|"docker build"| IMG["📦 Image (read-only layers)"]

    IMG --> STORE["💾 Local Image Store"]

    STORE -->|"docker push"| REG["🌐 Registry"]
    REG -->|"docker pull"| STORE

    subgraph RUNTIME[" "]
        direction LR
        C["🚀 Container (shared read-only layers + writable top layer)"]
        V["📁 Volume (persistent data)"]
        V -.-> C
    end

    STORE -->|"docker compose up (or docker run)"| C
```

## Dockerfile

## Basic

`Dockerfile` example:

``` dockerfile
FROM rocker/shiny:latest # <1>
RUN apt-get update # <2>
RUN apt-get install -qq -y libpq-dev # <2>
RUN install2.r ggplot2 shiny shinydashboard \
    shinydashboardPlus \
    dplyr RPostgres # <2>
WORKDIR /srv/shiny-server # <3>
COPY app/ . # <4>
ENV R_VERSION=4.4.0 # <5>
EXPOSE 3838 # <6>
ENTRYPOINT ["R", "--vanilla"] # <7>
CMD ["-e", "shiny::runApp('/srv/shiny-server', host='0.0.0.0', port=3838)"] # <8>
HEALTHCHECK --interval=30s --timeout=3s CMD curl -f http://localhost:3838/ || exit 1 # <9>
```

1.  `FROM` specifies the base image
2.  `RUN` executes a command in the container
3.  `WORKDIR` sets the working directory for all following instructions (like `cd` for the build)
4.  `COPY` copies files from the build context (directory passed to `docker build`) into the image
5.  `ENV` sets environment variables in the container (avoid secrets here; pass them at run time with `-e` or compose)
6.  `EXPOSE` only documents which ports the container listens on; publishing to the host still requires `-p`
7.  `ENTRYPOINT` defines the executable and fixed arguments when a container starts: here R with `--vanilla` (no saved workspace or startup profiles). The JSON (exec) form avoids an extra shell.
8.  `CMD` supplies default arguments appended to `ENTRYPOINT`: here `-e` and the Shiny expression. Together they start the app directly in R instead of the base image’s Shiny Server. `host='0.0.0.0'` makes the app reachable through a published port. Without an `ENTRYPOINT`, `CMD` would need to specify the executable too.
9.  `HEALTHCHECK` runs a command periodically (here every 30s) and marks the container `unhealthy` after repeated failures; the status shows up in `docker ps`. Requires `curl` in the image. Its `CMD` is separate from the container’s startup `CMD` and does not use `ENTRYPOINT`.

## Staged

Example of a multi-stage Python `Dockerfile`: install dependencies in a build stage, then copy only the virtual environment and application into the runtime image.

``` dockerfile
# ---- Build stage: install dependencies with uv ----
FROM ghcr.io/astral-sh/uv:python3.13-bookworm-slim AS build # <1>

ENV UV_COMPILE_BYTECODE=1 \
    UV_LINK_MODE=copy # <2>

WORKDIR /app

COPY pyproject.toml uv.lock ./
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --locked --no-install-project --no-dev # <3>

# ---- Runtime stage: slim Python, app + venv only ----
FROM python:3.13-slim-bookworm # <4>

WORKDIR /app

COPY --from=build /app/.venv /app/.venv # <5>
COPY --chmod=644 main.py backend.py ui.py ./ # <6>

RUN mkdir -p /data /opt/app-home \
    && chgrp -R 0 /data /opt/app-home \
    && chmod -R g=u /data /opt/app-home # <7>

USER 1001:0 # <8>

EXPOSE 3838 # <9>

CMD ["/app/.venv/bin/python", "main.py"] # <10>
```

1.  Base image with UV, python13 and slimmed down Debian OS (saves space); `AS build` lets later instructions copy files from this stage.
2.  Configure uv: `UV_COMPILE_BYTECODE=1` compiles installed Python files to bytecode during the build, which can improve startup time. `UV_LINK_MODE=copy` copies packages from uv’s cache into the virtual environment instead of linking them, keeping the environment independent of the cache mount.
3.  Install dependencies into `/app/.venv`. `--locked` to adhere to lock-file. `--no-install-project` skips installing your own project as a package; `--no-dev` excludes the `dev` dependency group. The BuildKit cache mount reuses uv downloads across builds. This cache is not included in the image.
4.  Start a fresh runtime stage with Python, using the same Python version and Debian release for compatibility.
5.  Copy the installed virtual environment from `build`, keeping its absolute path unchanged because installed scripts can reference that path.
6.  Copy the application scripts from the build context into `/app`. Permission mode `644` allows owner to read and write, and everyone else to read. The files remain root-owned, but the application user can read them; executable permission is unnecessary because Python runs them.
7.  This command creates two directories (`/data` and `/opt/app-home`), sets their group ownership to root (GID `0`), and ensures group permissions match user permissions (`g=u`).
8.  Run subsequent instructions and the container’s default command as the default non-root user (UID=`1001`), `0` sets group to root group (not the same as root permissions).
9.  Document that the application listens on port `3838`. Publish it with `docker run -p 3838:3838 ...` or Compose’s `ports`; the application must also listen on `0.0.0.0`, not just `127.0.0.1`.
10. Start `main.py` with the virtual environment’s Python interpreter. Using its absolute path avoids needing to activate the environment or modify `PATH`. The JSON (exec) form avoids an extra shell and lets Python receive container signals directly.

## Basic Docker Commands

| Description | Command |
|----|----|
| Start application | `docker run <container-name/container-id>` |
| List containers | `docker ps` |
| List docker images | `docker images` |
| Pull images from registry | `docker pull` |
| Kill container | `docker kill <container-name/container-id>` |
| Build image based on Dockerfile and tag it with a name | `docker build <dir-with-docker-file> -t my-service-name` |
| Execute command inside container | `docker exec <command> <container>` |

> **WARNING:**
>
> Arguments after the image name **replace the entire CMD**. - `docker run -rm -p 3838:3838 my-shiny-app` keeps the default CMD. - `docker run -rm my-shiny-app -e 'seesionInfo()'` replaces the entire CMD but keeps the ENTRYPOINT resulting in e.g. `R --vanilla -e 'sessionInfo()'`.

### Useful Options

Options can be combined, e.g. `docker run -it --rm <image>`:

| Option | Description |
|----|----|
| `--rm` | Automatically remove the container when it exits |
| `-it` | Interactive mode with a TTY (`-i` + `-t`), needed for shells |
| `-d` | Run container in the background (detached mode) |
| `-v` | Mount a volume or host directory into the container (e.g. `-v ./app:/srv/shiny-server`) |
| `-p` | Publish a container port to the host (e.g. `-p 3838:3838`) |
| `-e` | Set environment variables (e.g. `-e PASSWORD=secret`) |
| `--name` | Assign a custom name to the container (e.g. `--name shiny_dev`) |
| `--restart` | Set restart policy (e.g. `--restart always`) |
| `--init` | Safeguard for apps with subprocesses. Run a lightweight init process as PID 1 to forward signals and reap exited orphaned child processes. |

For Docker Compose equivalents (e.g. `container_name` for `--name`, `restart` for `--restart`, `ports` for `-p`, `volumes` for `-v`, `environment` for `-e`), see [Docker Compose](#docker-compose).

## Interacting with Containers

Start a container and execute bash:

``` bash
docker run -d rocker/shiny
docker exec -it rocker/shiny /bin/bash
```

Start R directly in a container:

``` bash
docker exec -it rocker/shiny /usr/local/bin/R
```

### Container Management

| Description | Command |
|----|----|
| Stop container | `docker stop <container-name/container-id>` |
| Remove container | `docker rm <container-name/container-id>` |
| Remove container and its volumes | `docker rm -v <container-name/container-id>` |
| Restart container | `docker restart <container-name/container-id>` |

## Docker Compose

Docker Compose is a tool for defining and running multi-container Docker applications using a YAML file.

Example `docker-compose.yml`:

``` yaml
services:
  postgres:
    container_name: db_container
    build: ./postgres # <1>
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
    ports:
      - "5432:5432" # <2>
    volumes:
      - "./pgdata:/var/lib/postgresql/data" # <3>
  
  shiny:
    container_name: shiny
    depends_on:
      - postgres
    build: ./shiny # <1>
    volumes:
      - "./shiny-logs:/var/log/shiny-server" # <3>
      - "./shiny-home:/srv/shiny-server" # <3>
    ports:
      - "3838:3838" # <2>
    cpus: 1.5 # <4>
    mem_limit: 2g # <4>
    memswap_limit: 2g # <4>
    init: true # <5>
```

1.  `build` points to the folder that contains the Dockerfile for this service; compose builds the image from it (alternative: `image:` to pull a ready-made image from a registry)
2.  `ports` maps `host:container` — traffic on host port 3838 is forwarded to container port 3838
3.  `volumes` maps `host-path:container-path` — a folder next to the docker-compose.yml on the host is mounted into the container (equivalent to `-v`); data survives container removal
4.  Resource limits the container can use: `mem_limit`: maximum RAM. `memswap_limit`: maximum RAM + swap to disk (can avoid OOM errors).
5.  `init: true` For apps spawning subprocesses: Runs a lightweight init process as PID 1, forwards signals to the application and reaps exited orphaned child processes.

### Starting and Recreating Containers

Start all services in the background:

``` bash
docker compose up -d
```

Normally, `up` recreates existing containers only when their configuration or image has changed. `--force-recreate` replaces them even if nothing changed.

### Useful Compose Options

These options belong to `docker compose up` and can be combined, e.g. `docker compose up -d --build`:

| Option | Description |
|----|----|
| `-d`, `--detach` | Run containers in the background |
| `--build` | Build service images before starting containers; uses the build cache |
| `--no-build` | Do not build images; required images must already exist or be pulled |
| `--pull always` | Pull service images from the registry before running (`missing` and `never` are other policies) |
| `--force-recreate` | Replace containers even if their configuration and image have not changed |
| `--no-recreate` | Keep existing containers even if configuration or images changed; incompatible with `--force-recreate` |
| `--no-deps` | Do not start dependencies of the selected services, e.g. `docker compose up -d --no-deps shiny` |
| `--wait` | Wait until services are running or healthy; implies detached mode. Define health checks to verify readiness |
| `--wait-timeout 60` | Limit the wait for readiness to 60 seconds |

Global options go **before** the subcommand:

| Option | Description |
|----|----|
| `-f <file>` | Select a Compose file; repeat to merge files in order, e.g. `docker compose -f compose.yaml -f compose.prod.yaml up -d` |
| `-p <name>` | Set the project name to separate application stacks |
| `--env-file <file>` | Load variables from referenced `xxx.env`-file. Variables are only used if referenced in compose file (e.g. `name: "${NAME_FROM_ENV_FILE}"`) |
| `--profile <name>` | Enable services assigned to a profile, e.g. `docker compose --profile debug up -d` if these services in the compose file have a field `profiles: ["debug"]` |

### Other Useful Compose Commands

| Command | Description |
|----|----|
| `docker compose ps -a` | Show all service containers, including stopped ones |
| `docker compose logs -f --tail=100` | Follow logs for all services; append a service name to filter |
| `docker compose exec shiny /bin/bash` | Open a shell in the running `shiny` service; use `/bin/sh` if Bash is unavailable |
| `docker compose run --rm shiny <command>` | Run a one-off command in a new container and remove it afterward; service ports are not published by default |
| `docker compose config` | Render and validate the resolved Compose configuration |
| `docker compose pull` | Pull service images without replacing running containers; follow with `up -d` to apply them |
| `docker compose build --no-cache` | Build images without cached layers; add `--pull` to also check for newer base images |
| `docker compose stop` | Stop containers without removing them |
| `docker compose down` | Stop and remove service containers and project networks; keep volumes by default |
| `docker compose down -v` | Also remove declared named volumes and attached anonymous volumes; **deletes their data**. External volumes and bind-mounted host files are not removed |

See the Docker CLI reference for [`compose up`](https://docs.docker.com/reference/cli/docker/compose/up/) and [`compose down`](https://docs.docker.com/reference/cli/docker/compose/down/).

## Debugging

Approach container debugging from the outside in:

1.  Check the container status
2.  Read container logs
3.  then get a shell inside
4.  Only inspect networks and volumes once the basics look fine.

If a container crashes immediately, keep it alive first so there is something to debug.

### Container Status

| Description | Command |
|----|----|
| List running containers (with grep filter) | `docker ps | grep <container-name>` |
| Show all containers including stopped ones | `docker ps -a` |

### Container Logs

View container logs:

``` bash
docker logs <container-name>
```

Options can be combined, e.g. `docker logs -f --tail=100 <container-name>`:

| Option    | Description                                          |
|-----------|------------------------------------------------------|
| `-f`      | Follow the log output live                           |
| `--tail`  | Show only the last N lines (e.g. `--tail=100`)       |
| `--since` | Show logs since a specific time (e.g. `--since 30m`) |

Prevent container from crashing for debugging: Add to the service in docker-compose.yml (clear any image entrypoint so `sleep` runs directly):

``` yaml
entrypoint: []
command: ["sleep", "infinity"]
```

### Inside the Container

| Description | Command |
|----|----|
| Get shell access inside container | `docker exec -it <container-name> /bin/bash` |
| Check container environment variables | `docker exec <container-name> env` |
| Check if container can reach internet | `docker exec <container-name> ping 8.8.8.8` |

### Container Information

| Description                         | Command                           |
|-------------------------------------|-----------------------------------|
| Show detailed container info        | `docker inspect <container-name>` |
| Show container resource usage       | `docker stats <container-name>`   |
| Show processes running in container | `docker top <container-name>`     |

### Network Debugging

| Description                 | Command                                 |
|-----------------------------|-----------------------------------------|
| Check port mappings         | `netstat -tulpn | grep <port-number>`   |
| Test container connectivity | `curl -v http://localhost:<port>`       |
| List all Docker networks    | `docker network ls`                     |
| Inspect network settings    | `docker network inspect <network-name>` |

### Volume Inspection

| Description | Command |
|----|----|
| List volumes | `docker volume ls` |
| Inspect volume | `docker volume inspect <volume-name>` |
| Check volume mount points | `docker inspect -f '{{ .Mounts }}' <container-name>` |
