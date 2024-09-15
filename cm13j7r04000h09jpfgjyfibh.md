---
title: "Run systemctl inside a docker container"
datePublished: Sun Sep 15 2024 12:07:45 GMT+0000 (Coordinated Universal Time)
cuid: cm13j7r04000h09jpfgjyfibh
slug: run-systemctl-inside-a-docker-container
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/HSACbYjZsqQ/upload/732523e86a5599c92820669b3e02921a.jpeg
tags: linux, docker, system-architecture

---

### 1\. **Use** `docker run` with `--privileged` and install systemd manually

You can use the `--privileged` flag when starting a container to grant it more privileges and install `systemd`. Here's a step-by-step guide:

1. **Create a Dockerfile** for an Ubuntu image with systemd installed.
    
    ```plaintext
    DockerfileCopy codeFROM ubuntu:20.04
    
    ENV container docker
    
    RUN apt-get update && \
        apt-get install -y systemd systemd-sysv && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*
    
    VOLUME [ "/sys/fs/cgroup" ]
    
    CMD ["/sbin/init"]
    ```
    
2. **Build the Docker image**:
    
    ```plaintext
    bashCopy codedocker build -t ubuntu-systemd .
    ```
    
3. **Run the container** with `--privileged` and mount necessary filesystems:
    
    ```plaintext
    bashCopy codedocker run --privileged -v /sys/fs/cgroup:/sys/fs/cgroup:ro -d ubuntu-systemd
    ```
    
4. Now, you should be able to **enter the container** and run `systemctl` commands:
    
    ```plaintext
    bashCopy codedocker exec -it <container_id> bash
    ```
    
    Inside the container, you can now run `systemctl` commands, such as:
    
    ```plaintext
    bashCopy codesystemctl status
    ```
    

### 2\. **Alternative: Using** `host` init system services

If your main goal is to control services inside the container, it's often better to manage services using `supervisord` or similar process managers instead of `systemctl` in Docker. This avoids the need to run `systemd` within the container, which can complicate the container's lightweight and isolated nature.

### 3\. **Docker Compose Approach (for multi-service containers)**

If your goal is to run multiple services inside a single container, you can use `docker-compose` to run multiple containers, each managing a different service.

For example, instead of using `systemd` inside Docker, you can define multiple services in your `docker-compose.yml` file:

```plaintext
yamlCopy codeversion: '3'
services:
  web:
    image: my-web-image
    ports:
      - "80:80"
  database:
    image: postgres
    volumes:
      - ./data:/var/lib/postgresql/data
```

### 4\. **Use** `systemd-docker`

There is a utility called `systemd-docker`, but it is an advanced option that allows `systemd` to run in a Docker container. It's not officially maintained, but you can explore it [here on GitHub](https://github.com/kinvolk/systemd-docker).

### Summary

Running `systemctl` inside Docker is not the typical use case because Docker doesn't use an init system like `systemd`. However, if you need `systemctl` for managing services, the method of using the `--privileged` flag with `systemd` in the container is a viable workaround. For more typical use cases, it is often better to manage services via `supervisord` or Docker Compose, avoiding `systemctl` entirely.