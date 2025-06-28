# Docker Compose Web Stack

This repository provides a Docker Compose setup to quickly spin up a full web-hosting stack, including FrankenPHP Caddy, Apache, PHP-FPM, databases, and various services.

## 🚀 Overview

- **FrankenPHP Caddy** as the public web server  
- **Apache + PHP-FPM** for sites relying on standard `.htaccess` rewrites (proxied through Caddy)  
- **MySQL**, **MongoDB**, **Meilisearch**, **Redis** for data storage and search  
- **FTP** for file transfers (manage via `ftp-config/users.env`)  
- **Web-based Code Editor** for in-browser development  
- **Vaultwarden** (Bitwarden-compatible) for self-hosted password management  

> **Note:**  
> This Compose file was originally tailored for a specific environment.  
> Feel free to remove any unwanted services from `docker-compose.yml`—otherwise Docker will attempt to start them (and fail, since their config/files aren’t provided).

---

## ⚙️ Configuration

1. **Clone this repository.**  
2. In the `ftp-config/` directory, rename:

   ```bash
   mv users.env.example users.env
   ```
3. Edit ftp-config/users.env to define your FTP users.
Format:
  ```bash
# username|password|home_directory|uid
alice|secret123|/var/www/vhosts/alice|1001 \
bob|anotherPass|/var/www/vhosts/bob|1002 \
```
4. (Optional) Open docker-compose.yml and remove any services you don’t need.

## 🏃 Usage
Bring all services up in detached mode:

```bash
docker compose up -d
```

View logs in real time:

```bash
docker compose logs -f
```

Stop and remove containers:

```bash
docker compose down
```

## 📦 Services Included

- **FrankenPHP Caddy Web Server**  
- **Apache** (behind Caddy for legacy `.htaccess` support)  
- **PHP-FPM**  
- **FTP** (configured via `ftp-config/users.env`)  
- **Redis**  
- **MySQL**  
- **MongoDB**  
- **Meilisearch**  
- **Web-based Code Editor**  
- **Vaultwarden Password Manager**  


## ❓ Troubleshooting

- **Service fails to start**
  Check logs with:
  ```bash
  docker-compose logs <service>
  ```
  and confirm you’ve removed any unneeded services and provided necessary config files.

- **FTP users not loading:**
  Ensure ftp-config/users.env exists (renamed from users.env.example) and has correct formatting.
- **Caddy proxy issues:**
  Verify your site definitions in caddy/Caddyfile and that Apache-backed sites are reachable on their internal ports.
