---
description: How to run project in a production deployment.
icon: arrows-to-circle
---

# Production Deployment Guide

### Prerequisites

#### Platform Requirements

* **Operating System**: Specify the Linux distribution (e.g., Ubuntu 20.04, CentOS 7, etc.).
* **Required Packages**:
  *   Install Git

      ```bash
      sudo apt update && sudo apt install -y git 
      ```
  * Install Docker Compose (latest version): [GUIDE HERE](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)
  * add user to docker group: `sudo usermod -aG docker $USER`
  *   Verify installation:

      ```bash
      docker --version
      docker compose --version
      ```
* Domain name connected to server IP
  * if using Duke VCM then setup an alias. If not setup a domain name as shown [here](../appendix.md#connect-domain-to-server-ip).



### Installation Steps (Production Deployment)

#### Server Setup

* update server and install certbot

```bash
sudo apt update
sudo apt install nginx -y

sudo apt install certbot python3-certbot-nginx -y
```

* If using DUKE VCM then run this to setup certbot&#x20;

```bash
// Some codesudo certbot --server https://locksmith.oit.duke.edu/acme/v2/directory --email NETID@duke.edu --agree-tos --no-eff-email -d YOURDOMAIN.COM
```

* if not using DUKE VCM:

```bash
sudo certbot certonly --webroot -w /var/www/certbot -d  YOURDOMAIN.COM
```

Nginx will run in docker and just needs to access the .pem and .key files that certbot made to ensure ssl. Make sure nginx is not running outside of docker

```bash
# Stop and disable NGINX on Ubuntu
sudo systemctl stop nginx      # Stop the service
sudo systemctl disable nginx   # Disable auto-start
sudo systemctl mask nginx      # Prevent manual starts
systemctl status nginx         # Verify status
```

take note of where certbot stores the .pem and .key files. It should be at on the local machine.

```
Certificate is saved at: /etc/letsencrypt/live/YOURDOMAIN.COM/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/YOURDOMAIN.COM/privkey.pem
```

Copy those paths and you will use them to edit the nginx config inside the github repo to pass into nginx.

setup the code next

#### Code Setup

1.  **Clone the Repository**

    ```bash
    git clone https://github.com/Mishmash-Abroad/studyabroad.git
    cd /studyabroad
    ```

**Edit Nginx Config**

inside the github repo:

replace lines 27 - 28 of /studyabroad/nginx/conf/default.prod.conf to be

```nginx

    # SSL Certificates (Let's Encrypt)
    ssl_certificate /etc/letsencrypt/live/YOURDOMAIN.COM/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/YOURDOMAIN.COM/privkey.pem;

```

update the host definitions in the same nginx conf file ( lines 13 / 24) to match your domain

```nginx

# Redirect HTTP to HTTPS
server {
    listen 80;
    listen [::]:80;
    server_name YOURDOMAIN.COM;

    location / {
        return 301 https://$server_name$request_uri;
    }
}

# HTTPS Server
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name YOURDOMAIN.COM;

```

update the CSRF cookie domain to match your domain

```nginx

        # CSRF and security settings
        proxy_cookie_path / "/; HTTPOnly; Secure";
        proxy_cookie_domain $host YOURDOMAIN.COM;
    }
```

**Start the Application**

make sure you are in ./studyabroad

* Build and run the application in Docker:
*   check that there are not leftover volumes with stale data

    ```bash
    docker compose -f docker-compose.prod.yml down
    docker volume rm $(docker volume ls -q)
    docker compose -f docker-compose.prod.yml up -d --build
    ```

1. **Check Logs**

```bash
docker compose logs -f
```



1. **Access the Application**
   *   Open the application in your browser:

       ```
       https://YOURDOMAIN.COM
       ```

***

