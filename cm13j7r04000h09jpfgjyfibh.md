---
title: "Run systemctl inside a docker container"
datePublished: Sun Sep 15 2024 12:07:45 GMT+0000 (Coordinated Universal Time)
cuid: cm13j7r04000h09jpfgjyfibh
slug: run-systemctl-inside-a-docker-container
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/HSACbYjZsqQ/upload/732523e86a5599c92820669b3e02921a.jpeg
tags: linux, docker, system-architecture

---

### Why `systemd` is tricky in Docker

`systemd` is designed to be a system init manager for an entire operating system, and Docker containers are designed to run a single process. This mismatch makes running `systemd` inside Docker non-trivial. However, it is possible with some adjustments, and I will walk you through the process.

### Prerequisites

* Ensure you have Docker installed on your machine.
    
* Familiarity with Docker commands.
    

### Step-by-Step Guide to Run `systemd` in an Ubuntu Docker Container

#### Step 1: Create a Dockerfile for an Ubuntu image with `systemd`

We will create a custom Dockerfile that installs `systemd`, and allows it to run inside the container.

1. **Create a new directory** for your Docker project:
    
    ```bash
    mkdir ubuntu-systemd
    cd ubuntu-systemd
    ```
    
2. **Create a Dockerfile** inside this directory. This file will define the Ubuntu container and include the necessary configuration to run `systemd`.
    
    ```
    nano Dockerfile
    ```
    
3. **Add the following content to the Dockerfile:**
    
    ```dockerfile
    FROM ubuntu:20.04
    
    ENV container docker
    
    # Install systemd, systemd-sysv, and other necessary packages
    RUN apt-get update && \
        apt-get install -y systemd systemd-sysv dbus sudo && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*
    
    # Create a system directory and volume mount for cgroups
    VOLUME [ "/sys/fs/cgroup" ]
    
    # Use systemd as the default command
    CMD ["/sbin/init"]
    ```
    
    * We are using the official Ubuntu 20.04 base image.
        
    * We install `systemd`, `systemd-sysv` (provides `telinit`), and `dbus` (required for `systemd`).
        
    * The `CMD ["/sbin/init"]` tells Docker to start `systemd` when the container starts.
        
4. **Save the Dockerfile** and exit the editor.
    

#### Step 2: Build the Docker image

Now that the Dockerfile is created, you need to build the image from it.

1. **Run the following command** to build your Docker image:
    
    ```bash
    docker build -t ubuntu-systemd .
    ```
    
    This command will use the Dockerfile in the current directory to build an image named `ubuntu-systemd`.
    

#### Step 3: Run the container with the necessary privileges

To run `systemd` inside Docker, you need to grant the container special privileges and mount certain filesystems.

**Run the container** with the `--privileged` flag and moun

```bash
docker run --privileged -v /sys/fs/cgroup:/sys/fs/cgroup:ro -d ubuntu-systemd
```

Explanation:

* `--privileged`: This flag gives the container access to all devices on the host, which is necessary for `systemd` to work properly.
    
* `-v /sys/fs/cgroup:/sys/fs/cgroup:ro`: This mounts the control group (`cgroup`) filesystem, which is required by `systemd`.
    
* `-d`: Run the container in detached mode.
    

1. **Verify the container is running**:
    
    Run the following command to list all running containers:
    
    ```bash
    docker ps
    ```
    
    You should see your container running.
    

#### Step 4: Access the running container

Now that your container is running `systemd`, you can access it and use `systemctl` inside the container.

1. **Enter the container**:
    
    ```bash
    docker exec -it <container_id> bash
    ```
    
    Replace `<container_id>` with the actual container ID from the `docker ps` output.
    
2. **Check if** `systemd` is running:
    
    Inside the container, run:
    
    ```bash
    systemctl
    ```
    
    If everything is set up correctly, you should see the output from `systemctl` showing the system services running.
    

#### Step 5: Managing services inside the container

Now that `systemd` is running inside the container, you can start and stop services as you would on a normal system.

1. **Start a service**:
    
    For example, if you wanted to start the `cron` service inside the container:
    
    ```bash
    systemctl start cron
    ```
    
2. **Check the status of a service**:
    
    ```bash
    systemctl status cron
    ```
    
3. **Enable a service to start on boot**:
    
    ```bash
    systemctl enable cron
    ```
    

---

### Troubleshooting

If you still face issues, here are some common problems and solutions:

* **Systemd failed to start**: Ensure you are using the `--privileged` flag and have mounted `/sys/fs/cgroup` properly.
    
* **Error: Couldn't find an alternative telinit implementation**: This usually means `systemd-sysv` is not installed. Ensure you have the package installed in the Dockerfile.
    
* **Permission issues**: Make sure you're running the container with the `--privileged` flag.
    

---

### Recap

1. Created a Dockerfile to install `systemd`.
    
2. Built the Docker image using `docker build`.
    
3. Ran the container with `--privileged` and the necessary mounts.
    
4. Accessed the container and used `systemctl` to manage services.