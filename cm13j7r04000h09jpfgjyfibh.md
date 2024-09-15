---
title: "Run systemctl inside a docker container"
datePublished: Sun Sep 15 2024 12:07:45 GMT+0000 (Coordinated Universal Time)
cuid: cm13j7r04000h09jpfgjyfibh
slug: run-systemctl-inside-a-docker-container
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/HSACbYjZsqQ/upload/732523e86a5599c92820669b3e02921a.jpeg
tags: linux, docker, system-architecture

---

# Why `systemd` is tricky in Docker

`systemd` is designed to be a system init manager for an entire operating system, and Docker containers are designed to run a single process. This mismatch makes running `systemd` inside Docker non-trivial. However, it is possible with some adjustments, and I will walk you through the process.

# Prerequisites

* Ensure you have Docker installed on your machine.
    
* Familiarity with Docker commands.
    

# Guide to Run `systemd` in an Ubuntu Docker Container

1. Create `Dockerfile` is required like below:
    

```dockerfile
FROM ubuntu:22.04

RUN echo 'root:root' | chpasswd
RUN printf '#!/bin/sh\nexit 0' > /usr/sbin/policy-rc.d
RUN apt-get update
RUN apt-get install -y systemd systemd-sysv dbus dbus-user-session
RUN printf "systemctl start systemd-logind" >> /etc/profile

ENTRYPOINT ["/sbin/init"]
/sbin/init is important to init systemd and enable systemctl.
```

/sbin/init is important to init systemd and enable systemctl.

2. Then build the system.
    

```bash
docker build -t chinhnd/ubuntu-systemd -f Dockerfile .
docker run -it --privileged --cap-add=ALL chinhnd/ubuntu-systemd
```

# Access the running container

Now that your container is running `systemd`, you can access it and use `systemctl` inside the container.

1. **Enter the container**:
    
    ```bash
    docker exec -it <container_id> bash
    ```
    
    Replace `<container_id>` with the actual container ID from the `docker ps` output.
    
    Then log in with root/root.
    
2. **Check if** `systemd` is running:
    
    Inside the container, run:
    
    ```bash
    systemctl
    ```
    
    If everything is set up correctly, you should see the output from `systemctl` showing the system services running.