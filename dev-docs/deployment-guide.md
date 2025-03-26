---
icon: arrows-to-circle
description: How to run project in a production deployment.
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
sudo certbot \
  --server https://locksmith.oit.duke.edu/acme/v2/directory \
  --email NETID@duke.edu \
  --agree-tos \
  --no-eff-email \
  -d YOURDOMAIN.COM
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
    cd ./studyabroad
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

This loads in the provided data.

* Build and run the application in Docker:

```bash
sudo docker compose -f docker-compose.prod.yml up -d --build
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

if code gets modified and you need to redeploy

first remove old volumes with old static files

rebuild contianers

<pre><code>sudo docker compose -f docker-compose.prod.yml down
<strong>sudo docker volume rm $(docker volume ls -q)
</strong>sudo docker compose -f docker-compose.prod.yml up -d --build
</code></pre>

## Backups Set Up Guide

To deploy your backups solution

### Step 1: Create a backup server

A backup server can be a separate device or a virtual computer. It is imperative that the backup server is not the same as the production server.

Once you have created the backup server, log in with a user and copy the backup_script.sh file into the home directory of the user. This can be done with SCP, copy and paste or any other method you like.

Example of the backup server vcm home directory
![image](https://github.com/user-attachments/assets/4187c9bc-164e-46af-8fe0-92bbfcfe3f97)

The backup_script works by taking in a path, a server name, and a number. The path is where to save the backup files, the server name is from which server to save backups from and the number indicates the number of backups to save in the path. The system will only retain the N latest backups in the folder.

Create a folder such as dev_backups, and within it create more folders for daily_backups, weekly_backups, and monthly_backups.

This is merely a template and can be changed according to your needs.
![image](https://github.com/user-attachments/assets/5aafed14-7d07-4894-8d84-3e10db6cc63e)

Create a cronjob for the backup script following the template below.

`15 18 * * * echo /home/vcm/backup_script.sh /home/vcm/dev_backups/daily_backups dev-mishmash.colab.duke.edu 7 >> /var/log/daily_backup_dev_script.log 2>&1`
![image](https://github.com/user-attachments/assets/eba8bb0e-7957-473b-b601-d907ed1e0984)

The base principle is that at a certain time of day, a backup will be copied from the production server to the backup server, and a certain day of the week, this process will happen, and a certain day of the month it will also happen. 

If you wish to follow the command above exactly, you must create a XXX_backup_dev_script.log files within /var/log/. This will be where any errors or bugs will be logged, aside from the email alerts. Make sure that these files are writable by the user of the crontab.

You can edit the parameters for path, servername and the numbers for how many backups to keep.

Then you can start cron!

### Step 2: Log into the production server and log into the backend container

Log onto the production server using the proper credentials

go into ~/studyabroad and access the backend container

`cd ~/studyabroad`


`docker exec -u 0 -it studyabroad-backend-1 bash`

Once you can access the backend container, initialize cron

`cron`

then run this

`crontab -e`

This will instantiate a cronfile to create a cronjob. Add this to the bottom of the file

`30 6 * * * root /app/run_backup.sh`

Here, this will run the backup script at 6:30am every day. You can change these values accordingly.

### Complete!

## Disaster Recovery

If you are trying to recover a system that has been lost.

### [Step 1: Follow the Production Deployment Guide](#production-deployment-guide)

### [Step 2: Follow the How to Reload a Backup Guide](#backup-guide.md#)

