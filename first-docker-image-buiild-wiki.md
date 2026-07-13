A minimal Docker container using Python's built in HTTP server.

### Directory Structure

```text
simple-web/
├── Dockerfile
└── index.html
```

### `index.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Simple Web Server</title>
</head>
<body>
    <h1>Captain Vaishnavu C V</h1>
</body>
</html>
```

### `Dockerfile`

```dockerfile
FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y python3 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY index.html .

EXPOSE 80

CMD ["python3", "-m", "http.server", "80"]
```

## Build the image

```bash
docker build -t simple-web:v1 .
```

## Run the container

```bash
docker run -d --name simple-web -p 80:80 simple-web:v1
```

## Test

```bash
curl http://localhost
```

or open

```text
http://<SERVER-IP>
```

Expected output:

```html
<h1>Captain Vaishnavu C V</h1>
```

## Useful Docker commands

View running containers:

```bash
docker ps
```

View logs:

```bash
docker logs simple-web
```

Stop:

```bash
docker stop simple-web
```

Remove:

```bash
docker rm simple-web
```

Remove image:

```bash
docker rmi simple-web:v1
```

## Single command rebuild

```bash
docker stop simple-web
docker rm simple-web
docker rmi simple-web:v1
docker build -t simple-web:v1 .
docker run -d --name simple-web -p 80:80 simple-web:v1
```

This produces a lightweight Ubuntu based container that serves `index.html` over HTTP on port 80 using Python's built in web server.
