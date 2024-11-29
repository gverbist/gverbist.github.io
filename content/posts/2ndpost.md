+++
title = "Cloudflare Tunnel and containers"
date = "2024-11-28T21:05:37+01:00"
dateFormat = "02-01-2006" # This value can be configured for per-post date formatting
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["linux", "docker", "self hosting"]
description = "How to setup a cloudflare tunnel using your docker-compose file"
showFullContent = false
readingTime = false
hideComments = false
+++
Securing access to internally hosted applications is crucial, especially for applications that shouldn’t be directly exposed to the internet. Cloudflare Tunnel provides a secure, encrypted connection by creating an outbound tunnel from your network to Cloudflare’s infrastructure, eliminating the need to open external ports. With Docker Compose, setting up a Cloudflare Tunnel becomes straightforward and manageable within a few configuration steps.

In this guide, we’ll walk through setting up Cloudflare Tunnel, configuring it with Docker Compose, and securing sensitive information using environment variables.

## Step 1: Set Up Your Cloudflare Tunnel

Create the Tunnel
Log in to your Cloudflare account, then go to Zero Trust > Access > Tunnels.
Click Create a Tunnel and provide a unique name for your tunnel. Naming the tunnel after your application or project can make it easier to manage.

Find the Tunnel Token
After creating the tunnel, Cloudflare generates a unique Tunnel Token. This token is required for authorizing the cloudflared service (Cloudflare’s connector) to establish a secure connection between Cloudflare and your application.
The token can be found on the Tunnel Details page under Credentials. Copy this token; you’ll need it to set up the tunnel securely in Docker Compose.

## Step 2: Securely Store the Tunnel Token

To prevent exposing sensitive information within your docker-compose.yaml file, store the TUNNEL_TOKEN in a .env file. Using environment variables in Docker Compose allows for a cleaner and more secure configuration, especially if you share or version your configuration files.

Create a .env file
In the root directory of your project (where your Docker Compose file is located), create a new file named .env.

Add the Tunnel Token
Open the .env file and add your Tunnel Token like this:
```
TUNNEL_TOKEN=your_cloudflare_tunnel_token_here
```
Replace your_cloudflare_tunnel_token_here with the actual token you copied from the Tunnel Details page.

This approach keeps your Docker Compose file free of sensitive data, as Docker Compose will automatically load variables from the .env file.

## Step 3: Configure Docker Compose with Cloudflare Tunnel

With the token securely stored, you’re ready to configure the Docker Compose file. The following example docker-compose.yaml file includes both your application and the cloudflared tunnel service.

```
services:
  app:
    image: your_app_image:latest  # Replace with your application’s Docker image
    ports:
      - "8080:8080"  # Map the application port

  cloudflared:
    image: cloudflare/cloudflared:latest
    command: tunnel --no-autoupdate run
    environment:
      - TUNNEL_TOKEN=${TUNNEL_TOKEN}
    restart: unless-stopped
```
### Explanation of the Configuration:

App Service: This service represents your internal application. Replace your_app_image with the Docker image for your app. Adjust the ports according to your application’s setup.

Cloudflared Service: The cloudflared service uses the official Cloudflare Docker image. The command tunnel --no-autoupdate run instructs Cloudflare to start and maintain the tunnel connection. The TUNNEL_TOKEN environment variable retrieves the token from the .env file, authorizing the secure tunnel.

Restart Policy: Setting restart: unless-stopped ensures the tunnel service automatically restarts if it stops unexpectedly, maintaining consistent connectivity.

## Step 4: Run Docker Compose

With your Docker Compose file configured, it’s time to deploy your application along with the Cloudflare Tunnel.

Start Docker Compose
Run the following command to start all services:
```
docker-compose up -d
```
The -d flag runs the services in detached mode, allowing them to continue running in the background.

Verify the Tunnel Connection
Open your Cloudflare dashboard, go to Zero Trust > Access > Tunnels, and you should see your tunnel listed as active.
You can now access your internal application through the secure Cloudflare Tunnel, with Cloudflare handling all HTTPS traffic and network security.

**Benefits of Using Cloudflare Tunnel with Docker Compose**

- Improved Security: By eliminating open ports and using a secure outbound tunnel, Cloudflare Tunnel reduces your application’s exposure and minimizes security risks.

- Automatic HTTPS: Cloudflare automatically applies HTTPS to all traffic routed through the tunnel, ensuring end-to-end encryption without extra configuration.

- Simplicity: Using Docker Compose to manage both your application and the tunnel service in a single configuration file simplifies deployment and maintenance.

## Final Thoughts

Integrating Cloudflare Tunnel with Docker Compose provides a robust solution for securely accessing internal applications. By leveraging a .env file to manage the Tunnel Token, you maintain security best practices while keeping your configuration modular and manageable. This setup offers a streamlined approach to secure application access and simplifies the configuration needed to protect sensitive resources behind a Cloudflare-managed tunnel.
