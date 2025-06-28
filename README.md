# Docker Compose Web Stack

This repository provides a Docker Compose setup to spin up a FrankenPHP Caddy web server and MySQL database to host your websites. For sites using standard `.htaccess` rewrite rules, Apache and PHP-FPM are included and proxied through Caddy. Caddy’s built-in Cloudflare DNS support allows you to set your Cloudflare API key in the environment and obtain SSL certificates via DNS-01 challenge.

---

## Caddyfile

Create a Caddyfile at `frankenphp/sites-enabled/domain.com/Caddyfile`:

```caddyfile
(reverse-proxy-domain-com) {
    import common-headers
    import common-tls
    encode zstd br gzip
    root * /var/www/vhosts/domain.com/httpdocs/public
    php_server
    file_server
    log {
        output file /var/log/caddy/domain.com.log
        format console
    }
}

domain.com:80 {
    import common-headers
    root * /var/www/vhosts/domain.com/httpdocs/public
    redir https://domain.com{uri}
}

www.domain.com:80 {
    import common-headers
    root * /var/www/vhosts/domain.com/httpdocs/public
    redir https://domain.com{uri}
}

domain.com:443 {
    import reverse-proxy-domain-com
}

www.domain.com:443 {
    redir https://domain.com{uri}
}

*.domain.com:443 {
    import reverse-proxy-domain-com
}
```

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
2. Rename .env.example to .env and set the following variables:
      ```bash
      mv .env.example .env
      ```

      ```dotenv
      CF_API_TOKEN=your_cloudflare_api_token
      # MySQL
      MYSQL_ROOT_PASSWORD=your_mysql_root_password
    
      # Cloudflare DNS (for Caddy DNS-01 challenge)
      CLOUDFLARE_API_TOKEN=your_cloudflare_api_token
    
      # Network IPs
      PRIVATE_NETWORK_IP=your_private_network_ip
      PUBLIC_NETWORK_IP=your_public_network_ip
   ```
3.In the `ftp-config/` directory, rename:

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
