# Docker on Proxmox LXC

This documents the process of getting Docker running inside a Debian 12 LXC container on Proxmox VE.

The main goal here was to start building out a lightweight environment for self-hosted services, CI/CD testing, containers, and general DevOps practice without needing a bunch of hardware.

---

# Environment

## Host System

* Lenovo ThinkCentre M920s
* Proxmox VE
* 16GB RAM
* SSD storage

## Container

* Debian 12 LXC
* Hostname: `dockerlab`
* 2 CPU cores
* 4GB RAM
* 32GB disk

---

# Creating the Container

Created a new Debian 12 LXC container through the Proxmox web UI.

Settings used:

| Setting                | Value              |
| ---------------------- | ------------------ |
| Container ID           | 100                |
| Hostname               | dockerlab          |
| Template               | Debian 12 Standard |
| Disk                   | 32GB               |
| CPU                    | 2 Cores            |
| Memory                 | 4096 MB            |
| Network                | DHCP on vmbr0      |
| Unprivileged Container | Disabled           |

I used a privileged container because Docker tends to work more smoothly inside LXCs that way.

---

# Enabling Docker Support

Docker inside LXC requires nesting support.

From the Proxmox host shell:

```bash
pct set 100 -features nesting=1
```

Then rebooted the container:

```bash
pct reboot 100
```

---

# Accessing the Container

The Proxmox web console for the container initially appeared blank even though the container itself was running.

Instead of using the web console, I accessed the container directly from the Proxmox host with:

```bash
pct enter 100
```

That worked reliably.

---

# Updating Debian

Inside the container:

```bash
apt update && apt upgrade -y
```

After all packages were properly upgraded via the proxmox host shell, I was able to access dockerlab's shell from its own console.


---

# Installing Docker

Installed curl first:

```bash
apt install curl -y
```

Installed Docker using Docker’s official install script:

```bash
curl -fsSL https://get.docker.com | sh
```

Verified Docker installation:

```bash
docker ps
```

---

# Deploying First Test Container

Deployed an Nginx container to test Docker networking and external access:

```bash
docker run -d --name nginx-test -p 8080:80 nginx
```

Checked the container IP:

```bash
hostname -I
```

Then accessed the container from the browser:

```text
http://<dockerlab-ip>:8080
```

Successfully reached the Nginx welcome page.

<img width="691" height="298" alt="Screenshot 2026-05-16 184138" src="https://github.com/user-attachments/assets/2c912983-c44f-4be2-a2ac-67872e7b2ab7" />

This confirmed:

* Docker was functioning correctly
* LXC nesting was working
* Container networking was working properly
* External browser access was functional

---

# Installing Docker Compose

Installed Docker Compose plugin:

```bash
apt install docker-compose-plugin -y
```

Verified installation:

```bash
docker compose version
```
# Using Docker Compose

Created a directory for Docker projects:

```bash
mkdir ~/homelab
cd ~/homelab
```

Created the compose file:

```bash
vim docker-compose.yml
```

```bash
services:
  nginx:
    image: nginx
    ports:
      - "8080:80"
    restart: unless-stopped

  whoami:
    image: traefik/whoami
    ports:
      - "8081:80"
    restart: unless-stopped
```

This stack deploys:

*An Nginx web server
*A lightweight container that displays networking and request information for testing

Deploying the Stack

Started the containers with:

docker compose up -d

Verified containers were running:

docker ps

<img width="1198" height="69" alt="Screenshot 2026-05-16 193306" src="https://github.com/user-attachments/assets/2e2246e8-6483-4429-a8ee-6bf6318a14d9" />


Testing Access

Retrieved the container IP address:

```bash
hostname -I
```

Accessed the services from the browser:

http://<dockerlab-ip>:8080
http://<dockerlab-ip>:8081

<img width="647" height="341" alt="Screenshot 2026-05-16 184129" src="https://github.com/user-attachments/assets/810deedb-4c1e-4901-9293-0bd5357cef4b" />
<img width="691" height="298" alt="Screenshot 2026-05-16 184138" src="https://github.com/user-attachments/assets/7c491618-2745-4a0c-8840-460c975eda9d" />

The Nginx welcome page loaded successfully, and the whoami container returned container and networking information correctly.
---

# Current Status

Working:

* Proxmox VE
* Debian 12 LXC
* Docker
* Docker Compose
* Nginx test container
* External networking

Planned next:

* GitLab CE
* GitLab Runner
* CI/CD pipelines
* Monitoring stack
* Reverse proxy
* Kubernetes / k3s

---

# Notes

One of the biggest things learned so far is that troubleshooting infrastructure issues is a huge part of the process. Several problems during setup ended up being related to:

* repository configuration
* container console behavior
* LXC nesting requirements
* hardware limitations

The goal of this lab is less about following tutorials exactly and more about building and troubleshooting a real environment over time.
