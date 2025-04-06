# 🚀 Nextcloud in Docker (with Host NGINX Reverse Proxy)

This repository provides a quick, production-ready way to run [Nextcloud](https://nextcloud.com/) in Docker with:

- **MariaDB**
- **Persistent volumes** (local folders)
- **Admin auto-setup**
- **Cron container for background jobs**
- **Reverse proxy via NGINX on the host machine**
- Optional HTTPS support via **Let's Encrypt**

---

## 🔧 Setup Instructions

### 1. Clone this repository

```bash
git clone https://github.com/your-user/nextcloud-docker.git
cd nextcloud-docker
```

---

### 2. Create the `.env` file

```bash
cp .env.example .env
nano .env
```

> Fill in your database and admin credentials.

---

### 3. Create required folders

```bash
mkdir -p db nextcloud/config nextcloud/data nextcloud/html nextcloud/apps
```

---

### 4. Start the containers

```bash
docker compose up -d
```

Wait 30–60 seconds. You can then access Nextcloud at the domain you set.

---

## 🌐 Accessing Nextcloud

By default, the container exposes port `8080`. You can access it via:

```
http://cloud.example.com
```

*(Replace with the value from your **`.env`** → **`NEXTCLOUD_TRUSTED_DOMAIN`**)*

---

## 🔁 Host NGINX Reverse Proxy Setup

To proxy from your public domain to the internal Nextcloud container:

### NGINX config (`/etc/nginx/sites-available/cloud.example.com`):

```nginx
server {
    listen 80;
    server_name cloud.example.com;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Enable config and restart NGINX:

```bash
ln -s /etc/nginx/sites-available/cloud.example.com /etc/nginx/sites-enabled/
nginx -t && systemctl reload nginx
```

---

## 🔒 Enabling HTTPS with Let's Encrypt

Install Certbot:

```bash
sudo apt install certbot python3-certbot-nginx
```

Then run:

```bash
sudo certbot --nginx -d cloud.example.com
```

This will automatically obtain a certificate and reload NGINX with HTTPS enabled.

---

## 📁 Project Structure

```
nextcloud-docker/
├── .env.example        # Example environment config
├── .gitignore          # Ignore secrets and volumes
├── docker-compose.yml  # Docker setup
├── README.md           # You're reading it :)
├── db/                 # MariaDB data
└── nextcloud/
    ├── config/         # Nextcloud config.php
    ├── data/           # User files
    ├── html/           # Main app files
    └── apps/           # External apps
```

---

## 🛠 Useful Commands

```bash
# Start containers
docker compose up -d

# Stop and remove containers
docker compose down

# View logs
docker compose logs -f
```

---

## 📜 License

MIT — use freely, modify, and share.

---

## 💬 Need Help?

Create an issue or open a PR if you want to improve something!

