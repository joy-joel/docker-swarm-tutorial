# docker_compose_build_image

This sample demonstrates how to use [`docker-compose`](https://docs.docker.com/compose/) to **build multiple images from the same repository** and how to pass build‑time arguments into the `Dockerfile`.  
Two very simple applications are included – one written in Python and one in Node.js – to highlight the pattern.

> ⚠️ This directory is *not* intended for production use; the apps simply print environment information and exit.  
> It’s a learning repository for building images using `docker-compose`.

---

## 📁 Directory structure

```
docker_compose_build_image/
├── app.js                # Node.js “app”
├── app.py                # Python “app”
├── docker-compose.yml    # Compose file defining two build services
├── Dockerfile.node       # Dockerfile used by node-app
└── Dockerfile.python     # Dockerfile used by python-app
```

---

## 🛠 Prerequisites

- Docker Engine & Docker Compose installed on your machine
- Basic familiarity with Dockerfiles and environment variables

---

## 🚀 How it works

Each service in `docker-compose.yml` uses the `build:` section to create an image from the local context.

```yaml
services:
  python-app:
    build:
      context: .
      dockerfile: Dockerfile.python
      args:
        - BASE_IMAGE=python:3.14
        - APP_VERSION=1.0.0
    environment:
      DEBUG: "true"
    image: oluebubechukwu/pythonwebapp:1.0.0


  node-app:
    build:
      context: .
      dockerfile: Dockerfile.node
      args:
        - BASE_IMAGE=node:latest
        - APP_NAME=node-service
    environment:
      DEBUG: "true"
      image: oluebubechukwu/node-webapp:latest
```

- **`context`** – build context is the current directory (`.`); both Dockerfiles copy the entire directory.
- **`dockerfile`** – selects which Dockerfile to use.
- **`args`** – build‑time arguments passed to `FROM` and used to set environment vars.
- **`environment`** – runtime environment variables (e.g. `DEBUG`).

The two Dockerfiles are nearly identical:

```dockerfile
ARG BASE_IMAGE
FROM ${BASE_IMAGE}

WORKDIR /app

ARG APP_VERSION          # or ARG APP_NAME in node Dockerfile
ENV APP_VERSION=${APP_VERSION}   # or ENV APP_NAME...

ENV ENVIRONMENT=production        # (or development)

COPY . .

CMD [ "python", "app.py" ]        # or [ "node", "app.js" ]
```

At runtime the apps read `APP_VERSION`/`APP_NAME` and `ENVIRONMENT` and write a log message.

---

## ⚡ Build & run

From the `docker_compose_build_image` directory:

```bash
# build both images
docker-compose build

# start containers
docker-compose up
```

Logs will show output similar to:

```
python-app    | Running App version 1.0.0 in production environment.
node-app      | Running node-service in development environment.
```

To rebuild with different build‑time values:

edit `docker-compose.yml` directly.

---

## Customization

- **Change the base image** via `BASE_IMAGE` build arg.
- **Set the application name or version** using `APP_NAME`/`APP_VERSION`.
- **Toggle environment** by editing the `ENVIRONMENT` variable in the Dockerfiles or by passing a runtime `environment:` in `docker-compose.yml`.
- Add further services by copying one of the service blocks and providing a new Dockerfile/app.

---

##  Notes

- Images are tagged as `oluebubechukwu/pythonwebapp:1.0.0` and `oluebubechukwu/node-webapp:latest` in the example; adjust the `image:` field to match your registry or dockerhub username.
- `docker-compose` handles the context for you, so changes to any file in the directory will be included on rebuild.
- The apps are intentionally simple; extend them to experiment with environment setup, multi-stage builds, or deployment to Docker Swarm.

---

Enjoy experimenting with building multi‑language images using Docker Compose.
