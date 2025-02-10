---
description: How to run project in a production deployment.
icon: arrows-to-circle
---

# Production Deployment Guide

### Prerequisites

#### Platform Requirements

* **Operating System**: Specify the Linux distribution (e.g., Ubuntu 20.04, CentOS 7, etc.).
* **Required Packages**:
  *   Install Git, Docker, and Docker Compose:

      ```bash
      sudo apt update && sudo apt install -y git docker.io
      ```
  *   Install Docker Compose (latest version):

      ```bash
      sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
      sudo chmod +x /usr/local/bin/docker-compose
      ```
  *   Verify installation:

      ```bash
      docker --version
      docker-compose --version
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

Copy those paths and you will use them to edit the docker config to pass into nginx.

setup the code next

#### Code Setup

1.  **Clone the Repository**

    ```bash
    git clone https://github.com/Mishmash-Abroad/studyabroad.git
    cd /studyabroad
    ```

**Edit Nginx Config**

once inside /studyabrod, edit the production docker compose file:

```
nano docker-compose.prod.yml
```

replace lines

1. **Start the Application**
   *   Build and run the application in Docker:

       ```bash
       docker compose up --build
       ```
2. **Access the Application**
   *   Open the application in your browser:

       ```
       http://localhost:8000
       ```

***

This guide ensures a smooth local deployment using Docker Compose. Let me know if you need further refinements! 🚀
