For the latest Kali Linux versions:

```bash
sudo apt update
sudo apt install -y docker.io
```

Enable and start the Docker service:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Verify the installation:

```bash
docker --version
sudo docker run hello-world
```

(Optional) Allow your user to run Docker without `sudo`:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Verify:

```bash
docker run hello-world
```

Useful Docker service commands:

```bash
# Check status
sudo systemctl status docker

# Start Docker
sudo systemctl start docker

# Stop Docker
sudo systemctl stop docker

# Restart Docker
sudo systemctl restart docker

# Enable at boot
sudo systemctl enable docker

# Disable at boot
sudo systemctl disable docker
```

If you also need Docker Compose:

```bash
sudo apt install -y docker-compose-v2
```

Verify:

```bash
docker compose version
```
