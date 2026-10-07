# Wazuh Open SIEM on Kali Linux via Docker

Beginner guide to deploy Wazuh Manager, Indexer and Dashboard on the same Kali Linux system using Docker.

> Example release: `v4.14.8`. Minimum recommended host: 4 CPU cores, 8 GB RAM and 50 GB disk.

## 1. Install Docker and Tools

1. **Refresh Kali packages:** `sudo apt update` updates the local package index.
2. **Install requirements:** `sudo apt install -y docker.io docker-compose git curl` installs Docker, Compose, Git and curl.
3. **Enable Docker:** `sudo systemctl enable docker --now` starts Docker now and on every reboot.
4. **Allow Docker without sudo:** `sudo usermod -aG docker $USER` adds the current user to the Docker group.
5. Log out and log back in after changing Docker group membership.
6. **Check Docker:** `docker --version` confirms Docker Engine is installed.
7. **Check Compose:** `docker compose version` confirms Docker Compose is available.

## 2. Configure Kali for Wazuh

8. **Set kernel map limit:** `sudo sysctl -w vm.max_map_count=262144` provides the memory mapping required by Wazuh Indexer.
9. **Keep the setting after reboot:** `echo 'vm.max_map_count=262144' | sudo tee /etc/sysctl.d/99-wazuh.conf` saves the kernel setting permanently.

## 3. Download Wazuh

10. **Clone Wazuh Docker:** `git clone https://github.com/wazuh/wazuh-docker.git -b v4.14.8` downloads the official release.
11. **Open the deployment folder:** `cd wazuh-docker/single-node` enters the single-node Compose directory.

## 4. Generate TLS Certificates

12. **Generate certificates:** `docker compose -f generate-indexer-certs.yml run --rm generator` creates self-signed certificates for the Wazuh components.

## 5. Start Wazuh

13. **Launch the SIEM:** `docker compose up -d` starts Wazuh Manager, Indexer and Dashboard in the background.
14. **Check containers:** `docker compose ps` shows the current status of all Wazuh services.
15. **Watch logs:** `docker compose logs -f` displays live startup and error logs.
16. Press `Ctrl+C` to leave the live log view without stopping Wazuh.

## 6. Dashboard Access

- Local access: `https://localhost`
- Remote access: `https://<KALI-IP>`
- **Find Kali IP:** `hostname -I` displays the host IP addresses.
- Default username: `admin`
- Default password: `SecretPassword`
- A browser certificate warning is normal when using the default self-signed certificates.

## 7. Important Ports

| Port | Purpose |
|---|---|
| 443/TCP | Wazuh Dashboard HTTPS |
| 1514/TCP | Agent event communication |
| 1515/TCP | Agent enrollment |
| 514/UDP | Syslog |
| 55000/TCP | Wazuh API |
| 9200/TCP | Wazuh Indexer API |

## 8. Basic Management Commands

17. **Stop Wazuh:** `docker compose stop` stops containers while preserving their data.
18. **Start Wazuh:** `docker compose start` starts previously stopped containers.
19. **Restart Wazuh:** `docker compose restart` restarts all services.
20. **Remove the running stack:** `docker compose down` removes containers and the Compose network while keeping persistent volumes.
21. **View recent errors:** `docker compose logs --tail=100` prints the latest 100 log lines.
22. **View resource usage:** `docker stats` displays CPU and memory usage for running containers.

> Do not run `docker compose down -v` unless you intentionally want to remove persistent Wazuh volumes and data.

## 9. Security Notes

- Change the default `admin` password before using this deployment outside a lab.
- Do not expose ports `9200` or `55000` directly to the Internet.
- Use a firewall and trusted network access for SIEM administration.
- Initial Wazuh Indexer startup can take about one minute.
- Dockerized Wazuh central components do not fully monitor the Kali host itself.
- Install a Wazuh agent separately if you want detailed monitoring of the Kali host.

## 10. Official References

- Wazuh Docker deployment: https://documentation.wazuh.com/current/deployment-options/docker/wazuh-container.html
- Wazuh Docker repository: https://github.com/wazuh/wazuh-docker
- Wazuh documentation: https://documentation.wazuh.com/
- Kali Docker installation: https://www.kali.org/docs/containers/installing-docker-on-kali/
