---
title: "Secure Your Web Apps in Minutes: OpenAppSec WAF with NGINX Proxy Manager"
datePublished: Thu Oct 16 2025 10:33:41 GMT+0000 (Coordinated Universal Time)
cuid: cmgta8455000c02js1ja94ifb
slug: secure-your-web-apps-in-minutes-openappsec-waf-with-nginx-proxy-manager
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/tZc3vjPCk-Q/upload/328c83285b5cfcee2682486de1c06a27.jpeg
tags: web, security, waf

---

With **OpenAppSec**, a modern, open-source WAF designed for simplicity and powerful, pre-emptive threat protection. When combined with the popular **NGINX Proxy Manager (NPM)**, you get a robust, easy-to-use solution for securing your containerized applications.

This guide will walk you through deploying OpenAppSec with NGINX Proxy Manager using Docker, all managed from the slick OpenAppSec SaaS Web UI. Let's get started!

# Prerequisites

Before we begin, ensure you have the following ready:

* A Linux server or VM with Docker and Docker Compose installed.
    
* Internet access to pull Docker images and connect to the OpenAppSec cloud.
    
* (Optional) A backend web application you want to protect. For this guide, we'll assume an application is running on port `3000`.
    

# Downloads setup files

Create a folder for your new open-appsec deployment and switch to that folder, e.g.

```bash
mkdir open-appsec-deployment
cd ./open-appsec-deployment
```

Download the docker compose file for your desired open-appsec integration

```bash
wget https://raw.githubusercontent.com/openappsec/openappsec/main/deployment/docker-compose/nginx-proxy-manager-centrally-managed/docker-compose.yaml
```

Download the `.env` file for your desired open-appsec integration and adjust the configuration to your requirements as described below:

```bash
wget https://raw.githubusercontent.com/openappsec/openappsec/main/deployment/docker-compose/nginx-proxy-manager-centrally-managed/.env
```

# Link Your Agent to the OpenAppSec Cloud

To manage our new WAF, we need to connect it to the central management portal. This is done using a secure token:

1. Navigate to the OpenAppSec Portal: [https://my.openappsec.io/](https://my.openappsec.io/)
    
2. Sign up for a free account or log in using your email, Google, or GitHub.
    
3. From the "Getting Started" page, follow these steps:
    
4. Check the box for "I deployed an Agent".
    
5. Click Manage and select the Docker Profile.
    
6. For the subtype, choose "NGINX Proxy Manager application security".
    
7. Click Enforce Policy.
    
8. You will now be presented with a unique management token. **Copy this token.**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1760608476204/5b478979-6166-4311-8acf-fe59eae3fc7c.png align="center")

Back in your server's terminal, add this token as an environment variable. This allows Docker Compose to inject it into the agent container upon startup.

(Remember to replace YOUR\_TOKEN\_HERE with the actual token you copied.)

```bash
export TOKEN="YOUR_TOKEN_HERE"
```

# Launch the Stack!

With the configuration in place, it's time to bring everything online. Run the following command to start the services in detached mode (`-d`), which lets them run in the background.

```bash
docker-compose up -d
```

Docker will now pull the necessary images and start the containers. To verify that everything is running correctly, use the `docker ps` command:

```bash
docker ps -a
```

You should see an output similar to this, with the `appsec-agent` and `npm-centrally-managed-attachment` containers showing an "Up" status:

```bash
CONTAINER ID   IMAGE                                                                        COMMAND                  CREATED          STATUS          PORTS                                                                                  NAMES
5204903ec4fe   ghcr.io/openappsec/nginx-proxy-manager-centrally-managed-attachment:latest   "/init"                  9 seconds ago    Up 6 seconds    0.0.0.0:80-81->80-81/tcp, :::80-81->80-81/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   npm-centrally-managed-attachment
4905a185b6fd   ghcr.io/openappsec/agent:latest                                              "/cp-nano-agent --to…"   9 seconds ago    Up 7 seconds                                                                                           appsec-agent
```

You should now see the application on the [open-appsec Web UI](https://my.openappsec.io/?utm_medium=playground&utm_source=instruqt&utm_content=management)!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1760609388653/0412fc55-3a8c-428d-83d1-d8eb86f4231e.png align="center")

# Configure Your First Proxy Host

Now that the stack is running, we need to tell NGINX Proxy Manager how to route traffic to your backend application.

Access the NGINX Proxy Manager web portal at `http://<your-server-ip>:81`.

On your first login, you'll be prompted to create an admin user and change the password.

[![image.png](https://play.instruqt.com/assets/tracks/qrqhoa8sdfw6/b8a6d6b9e70a1b18433ef2a4756df318/assets/image.png align="left")](https://play.instruqt.com/assets/tracks/qrqhoa8sdfw6/b8a6d6b9e70a1b18433ef2a4756df318/assets/image.png)

Click **Add Proxy Host**.

Fill in the details for your application. For example, if you have an app named `acmeaudit` running on the same Docker host:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1760609194847/9f4d285b-82bd-4791-8f1c-29c0cdbec96a.png align="center")

**Domain Names**: `acmeaudit.local` (or your actual domain)

**Scheme**: `http`

**Forward Hostname / IP**: `acmeaudit`

**Forward Port**: `3000`

Click **Save**.

Now, you can run the following curl command again and that NGINX Proxy Manager is now serving ACME Audit application on port 80:

```bash
curl http://localhost
```

Activate Protection in the OpenAppSec Portal

In the [open-appsec Web UI](https://my.openappsec.io/?utm_medium=playground&utm_source=instruqt&utm_content=management): Create an asset defining the specific resources that open-appsec should protect, don't forget to enforce the policy afterwards. When logged in to the open-appsec portal, click on the Assets option in the top navigation menu.

The final and most crucial step is to enable the WAF policy for your application.

Return to your **OpenAppSec Web UI**.

Click on the **Assets** tab in the top navigation menu.

Click **Create a new asset** and fill in the details:

**Profile**: Choose the Docker profile you created earlier.

**Web application URL**: To protect all traffic to your site, enter [`http://*/*`](http://*/*). You can make this more specific if needed.

[![image.png](https://play.instruqt.com/assets/tracks/yu4tfhxv43ox/7621956f69a37638bb3f89e2a9e501e3/assets/image.png align="left")](https://play.instruqt.com/assets/tracks/yu4tfhxv43ox/7621956f69a37638bb3f89e2a9e501e3/assets/image.png)

Switch to the **Web** tab within the asset configuration.

Change the **Threat Prevention Mode** from *Detect* to **Prevent**. This tells the WAF to actively block malicious traffic, not just log it.

[![image.png](https://play.instruqt.com/assets/tracks/61dt18yzccgr/2213b216630b0661bc9784b4a2f59d5a/assets/image.png align="left")](https://play.instruqt.com/assets/tracks/61dt18yzccgr/2213b216630b0661bc9784b4a2f59d5a/assets/image.png)

Click the **Enforce policy** button in the top right corner.

That's it! Your application is now actively protected by the OpenAppSec WAF. Any changes you make to the security policy in the SaaS portal will be automatically synced to your agent.

# Conclusion

You have successfully deployed a powerful, cloud-managed Web Application Firewall in front of your application using Docker. This combination of OpenAppSec and NGINX Proxy Manager provides a streamlined, modern, and highly effective way to manage your web security posture.

From here, you can explore the OpenAppSec portal to fine-tune security rules, monitor traffic, and analyze security events across all your protected assets.