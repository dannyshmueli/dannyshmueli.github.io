---
title: Install n8n on Oracle Free Tier
date: 2025-06-12 18:40:58
tags:
categories: [cloud, oracle-cloud, self-hosting, n8n]
index_img: /img/n8n-small.jpeg
banner_img: /img/n8n.jpeg

---

> **Note:** This guide is based on and inspired by [Self-hosted n8n with Oracle Cloud](https://medium.com/@dcheng857/self-hosted-n8n-with-oracle-cloud-7825e8aa62d7) by @dcheng857 on Medium. Please check out the original article for more details and credit.

# Setting up Oracle Cloud Free Tier and User

Before installing n8n, you need to set up your Oracle Cloud Free Tier account and create a VM instance:

## 1. Sign Up for Oracle Cloud Free Tier
- Go to [Oracle Cloud Free Tier](https://www.oracle.com/cloud/free/) and sign up for a free account.
- Complete the registration process and verify your email and payment method (no charges for Free Tier usage).

## 2. Create a Virtual Machine (VM) Instance
- Log in to the [Oracle Cloud Console](https://cloud.oracle.com/).
- Navigate to **Compute > Instances** and click **Create Instance**.
- Enter a name for your instance.
- Choose the **Always Free-eligible** shape (e.g., "VM.Standard.E2.1.Micro").
- Add your SSH public key (generate one with `ssh-keygen` if you don't have one).
- Click **Create** and wait for the instance to be provisioned.

### 2.1. Open Ingress Rules for Ports 80 and 443
To allow web traffic to your n8n instance, you need to open ports 80 (HTTP) and 443 (HTTPS) in your Oracle Cloud network settings:
- In the Oracle Cloud Console, go to **Networking > Virtual Cloud Networks** and select the VCN your instance is using.
- Click on the **Subnet** your instance is attached to.
- Under **Security Lists**, click the security list associated with your subnet.
- Click **Add Ingress Rules** and add two rules:
    - **Source CIDR:** 0.0.0.0/0
    - **IP Protocol:** TCP
    - **Destination Port Range:** 80 (for HTTP)
    - Repeat for port 443 (for HTTPS)
- Save the rules. Your instance will now accept incoming traffic on ports 80 and 443.

## 3. Connect to Your VM
- Find the public IP address of your instance in the Oracle Cloud Console.
- Connect via SSH:

```bash
ssh -i /path/to/your/private/key opc@<your_public_ip>
```
- The default user is `opc`. You can create a new user if desired:

```bash
sudo adduser yourusername
sudo usermod -aG sudo yourusername
```
- (Optional) Copy your SSH key to the new user:

```bash
sudo mkdir -p /home/yourusername/.ssh
sudo cp ~/.ssh/authorized_keys /home/yourusername/.ssh/
sudo chown -R yourusername:yourusername /home/yourusername/.ssh
```

---

# Setting up n8n with Docker, NGINX, and SSL on Ubuntu

This guide walks through setting up a production-ready n8n instance with Docker, NGINX as a reverse proxy, and SSL certificates using Let's Encrypt. We'll also configure some additional features like the Brave Search API integration.

## Prerequisites

- Ubuntu 20.04 LTS server
- A domain pointed to your server (in this guide we use n8n.dannyshmueli.com)
- Basic command line knowledge

## Step 1: Install Docker

First, let's install Docker which we'll use to run n8n:

```bash
sudo apt update
sudo apt install docker.io
sudo systemctl start docker
sudo systemctl enable docker
```

## Step 2: Install and Configure NGINX

Install NGINX to act as our reverse proxy:

```bash
sudo apt update && sudo apt install -y nginx
```

Create an NGINX configuration file for n8n:

```bash
sudo tee /etc/nginx/sites-available/n8n.conf >/dev/null <<'EOF'
server {
    listen 80;
    server_name n8n.dannyshmueli.com;

    location / {
        proxy_pass         http://127.0.0.1:5678;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection "upgrade";
        chunked_transfer_encoding off;
        proxy_buffering off;
        proxy_cache off;
    }
}
EOF
```

Enable the configuration:

```bash
sudo ln -s /etc/nginx/sites-available/n8n.conf /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx
```

## Step 3: Configure Firewall

Allow HTTP and HTTPS traffic:

```bash
sudo iptables -I INPUT -p tcp --dport 80  -j ACCEPT
sudo iptables -I INPUT -p tcp --dport 443 -j ACCEPT
```

## Step 4: Set Up SSL with Let's Encrypt

Install Certbot and get SSL certificates:

```bash
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d n8n.dannyshmueli.com
```

## Step 5: Run n8n with Docker

Now we'll run n8n with proper configuration including authentication and webhook support:

```bash
# Create n8n data directory and set permissions
mkdir -p ~/.n8n
sudo chown -R 1000:1000 ~/.n8n

# Run n8n container
sudo docker run -d --name n8n \
  -p 5678:5678 \
  -v /home/ubuntu/.n8n:/home/node/.n8n \
  -e N8N_HOST="n8n.dannyshmueli.com" \
  -e WEBHOOK_URL="https://n8n.dannyshmueli.com/" \
  -e N8N_SOCKET_IO_URL="wss://n8n.dannyshmueli.com/rest/push" \
  -e N8N_BASIC_AUTH_USER="your-email@example.com" \
  -e N8N_BASIC_AUTH_PASSWORD="your-strong-password" \
  --restart unless-stopped \
  n8nio/n8n
```

## Step 6: Additional Configuration - Brave Search API Integration

If you're planning to use the Brave Search API with n8n, you can add the API key to the Docker configuration:

```bash
sudo docker stop n8n && sudo docker rm n8n

sudo docker run -d --name n8n \
  -p 5678:5678 \
  -v /home/ubuntu/.n8n:/home/node/.n8n \
  -e N8N_HOST="n8n.dannyshmueli.com" \
  -e WEBHOOK_URL="https://n8n.dannyshmueli.com/" \
  -e N8N_SOCKET_IO_URL="wss://n8n.dannyshmueli.com/rest/push" \
  -e N8N_BASIC_AUTH_USER="your-email@example.com" \
  -e N8N_BASIC_AUTH_PASSWORD="your-strong-password" \
  -e BRAVE_API_KEY="your-brave-api-key" \
  --restart unless-stopped \
  n8nio/n8n
```

## Maintenance and Troubleshooting

### Viewing Logs

To check n8n logs:

```bash
sudo docker logs n8n
```

### Restarting n8n

To restart the n8n container:

```bash
sudo docker restart n8n
```

### Updating n8n

To update to the latest version:

```bash
sudo docker stop n8n; sudo docker rm n8n
# Pull the latest image and run with your configuration
sudo docker pull n8nio/n8n
sudo docker run -d --name n8n \
  -p 5678:5678 \
  -v /home/ubuntu/.n8n:/home/node/.n8n \
  -e N8N_HOST="n8n.dannyshmueli.com" \
  -e WEBHOOK_URL="https://n8n.dannyshmueli.com/" \
  -e N8N_SOCKET_IO_URL="wss://n8n.dannyshmueli.com/rest/push" \
  -e N8N_BASIC_AUTH_USER="your-email@example.com" \
  -e N8N_BASIC_AUTH_PASSWORD="your-strong-password" \
  --restart unless-stopped \
  n8nio/n8n
```

**Note:** Replace the environment variables with your actual configuration values. If you're using the Brave Search API integration, include the `BRAVE_API_KEY` environment variable as well.

## Security Considerations

1. Always use strong passwords for n8n authentication
2. Keep your system and Docker updated
3. Regularly backup your ~/.n8n directory
4. Use HTTPS only (configured through NGINX and Let's Encrypt)

## Common Issues and Solutions

### Permission Issues

If you encounter permission issues with the n8n directory:

```bash
sudo chown -R 1000:1000 /home/ubuntu/.n8n
```

### WebSocket Connection Issues

If you have issues with WebSocket connections, ensure your NGINX configuration includes the proper WebSocket headers (as shown in the NGINX configuration above).

## Conclusion

You now have a production-ready n8n instance running with:
- HTTPS encryption
- Basic authentication
- Webhook support
- WebSocket support
- Automatic container restart
- Brave Search API integration

Remember to regularly backup your data and keep your system updated for security and stability.