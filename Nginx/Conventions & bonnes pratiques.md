# Conventions & Bonnes Pratiques Nginx

## 📋 Structure de Base

### Configuration principale

```nginx
# /etc/nginx/nginx.conf

user www-data;
worker_processes auto;
pid /run/nginx.pid;

events {
    worker_connections 1024;
    use epoll;
    multi_accept on;
}

http {
    ##
    # Basic Settings
    ##
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    server_tokens off;  # Cache la version Nginx

    # Limites
    client_max_body_size 20M;
    client_body_buffer_size 128k;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ##
    # SSL Settings
    ##
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384;

    ##
    # Logging
    ##
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    ##
    # Gzip Settings
    ##
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_types text/plain text/css text/xml text/javascript
               application/json application/javascript application/xml+rss;

    ##
    # Virtual Host Configs
    ##
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

---

## 🌐 Configuration de Site

### Site statique (HTML/CSS/JS)

```nginx
# /etc/nginx/sites-available/mysite.com

server {
    listen 80;
    listen [::]:80;
    server_name mysite.com www.mysite.com;

    # Redirection HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name mysite.com www.mysite.com;

    # SSL Certificates
    ssl_certificate /etc/letsencrypt/live/mysite.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/mysite.com/privkey.pem;

    # Root directory
    root /var/www/mysite.com/html;
    index index.html index.htm;

    # Logs
    access_log /var/log/nginx/mysite.com.access.log;
    error_log /var/log/nginx/mysite.com.error.log;

    # Main location
    location / {
        try_files $uri $uri/ =404;
    }

    # Cache static files
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
}
```

### Reverse Proxy (Node.js, etc.)

```nginx
server {
    listen 80;
    server_name api.mysite.com;

    # Redirection HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name api.mysite.com;

    ssl_certificate /etc/letsencrypt/live/api.mysite.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/api.mysite.com/privkey.pem;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;

        # Headers
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_cache_bypass $http_upgrade;

        # Timeouts
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }
}
```

### SPA (React, Vue, Angular)

```nginx
server {
    listen 443 ssl http2;
    server_name app.mysite.com;

    ssl_certificate /etc/letsencrypt/live/app.mysite.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/app.mysite.com/privkey.pem;

    root /var/www/app.mysite.com/dist;
    index index.html;

    # SPA - toutes les routes vers index.html
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Cache des assets
    location ~* ^.+\.(jpg|jpeg|gif|png|ico|css|js|svg|woff|woff2|ttf)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Pas de cache pour index.html
    location = /index.html {
        add_header Cache-Control "no-cache, no-store, must-revalidate";
    }
}
```

---

## 🔒 SSL/TLS avec Let's Encrypt

### Installation Certbot

```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx
```

### Obtenir certificat

```bash
# Pour un domaine
sudo certbot --nginx -d mysite.com -d www.mysite.com

# Renouvellement automatique
sudo certbot renew --dry-run

# Cron pour auto-renewal
sudo crontab -e
# Ajouter:
0 12 * * * /usr/bin/certbot renew --quiet
```

### Configuration SSL optimale

```nginx
# /etc/nginx/snippets/ssl-params.conf

ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers on;
ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384;
ssl_ecdh_curve secp384r1;
ssl_session_timeout 10m;
ssl_session_cache shared:SSL:10m;
ssl_session_tickets off;
ssl_stapling on;
ssl_stapling_verify on;

resolver 8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout 5s;

# Headers de sécurité
add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;
add_header X-Frame-Options DENY always;
add_header X-Content-Type-Options nosniff always;
add_header X-XSS-Protection "1; mode=block" always;

# Utilisation dans server block
server {
    listen 443 ssl http2;

    ssl_certificate /path/to/cert;
    ssl_certificate_key /path/to/key;

    include snippets/ssl-params.conf;
}
```

---

## ⚡ Load Balancing

```nginx
# Upstream servers
upstream backend {
    # Algorithmes de load balancing:
    # - round-robin (défaut)
    # - least_conn (moins de connexions)
    # - ip_hash (même IP → même serveur)

    least_conn;

    server 192.168.1.10:3000 weight=3;  # Plus de requêtes
    server 192.168.1.11:3000;
    server 192.168.1.12:3000 backup;    # Backup
    server 192.168.1.13:3000 down;      # Temporairement désactivé
}

server {
    listen 80;
    server_name api.mysite.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

# Health checks (Nginx Plus)
upstream backend {
    zone backend 64k;
    server backend1.example.com;
    server backend2.example.com;

    health_check interval=10s fails=3 passes=2;
}
```

---

## 🚫 Rate Limiting

```nginx
# Dans http block
http {
    # Zone de rate limiting
    limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;
    limit_req_zone $binary_remote_addr zone=login_limit:10m rate=5r/m;

    server {
        # API rate limit
        location /api/ {
            limit_req zone=api_limit burst=20 nodelay;
            proxy_pass http://backend;
        }

        # Login rate limit
        location /login {
            limit_req zone=login_limit burst=5;
            proxy_pass http://backend;
        }
    }
}

# Limit par connexions simultanées
http {
    limit_conn_zone $binary_remote_addr zone=conn_limit:10m;

    server {
        location /download/ {
            limit_conn conn_limit 10;  # Max 10 connexions par IP
        }
    }
}
```

---

## 📦 Caching

### Proxy Cache

```nginx
# Dans http block
http {
    proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m
                     max_size=1g inactive=60m use_temp_path=off;

    server {
        location / {
            proxy_pass http://backend;

            # Cache
            proxy_cache my_cache;
            proxy_cache_valid 200 60m;
            proxy_cache_valid 404 10m;
            proxy_cache_use_stale error timeout updating http_500 http_502 http_503 http_504;
            proxy_cache_bypass $http_cache_control;

            add_header X-Cache-Status $upstream_cache_status;
        }

        # Purge cache (si module installé)
        location ~ /purge(/.*) {
            allow 127.0.0.1;
            deny all;
            proxy_cache_purge my_cache $1;
        }
    }
}
```

### FastCGI Cache (PHP)

```nginx
http {
    fastcgi_cache_path /var/cache/nginx/fastcgi levels=1:2 keys_zone=php_cache:10m
                       max_size=1g inactive=60m;

    server {
        location ~ \.php$ {
            fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
            fastcgi_index index.php;

            # Cache
            fastcgi_cache php_cache;
            fastcgi_cache_valid 200 60m;
            fastcgi_cache_bypass $http_cache_control;

            include fastcgi_params;
        }
    }
}
```

---

## 🔐 Sécurité

### Protection basique

```nginx
# Bloquer User-Agents
if ($http_user_agent ~* (bot|crawler|spider)) {
    return 403;
}

# Bloquer IPs
deny 192.168.1.1;
deny 10.0.0.0/24;
allow all;

# Authentification basique
location /admin {
    auth_basic "Restricted Area";
    auth_basic_user_file /etc/nginx/.htpasswd;
}

# Créer .htpasswd
# sudo apt install apache2-utils
# sudo htpasswd -c /etc/nginx/.htpasswd username
```

### CORS

```nginx
location /api/ {
    if ($request_method = 'OPTIONS') {
        add_header 'Access-Control-Allow-Origin' '*';
        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS, PUT, DELETE';
        add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range,Authorization';
        add_header 'Access-Control-Max-Age' 1728000;
        add_header 'Content-Type' 'text/plain; charset=utf-8';
        add_header 'Content-Length' 0;
        return 204;
    }

    add_header 'Access-Control-Allow-Origin' '*' always;
    add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS, PUT, DELETE' always;
    add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range,Authorization' always;
    add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range' always;

    proxy_pass http://backend;
}
```

---

## 🛠️ Commandes Utiles

```bash
# Tester la configuration
sudo nginx -t

# Recharger la configuration
sudo nginx -s reload

# Redémarrer Nginx
sudo systemctl restart nginx

# Status
sudo systemctl status nginx

# Logs en temps réel
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log

# Activer un site
sudo ln -s /etc/nginx/sites-available/mysite.com /etc/nginx/sites-enabled/

# Désactiver un site
sudo rm /etc/nginx/sites-enabled/mysite.com
sudo nginx -s reload
```

---

## 📊 Monitoring

### Stub Status

```nginx
server {
    listen 8080;
    server_name localhost;

    location /nginx_status {
        stub_status on;
        access_log off;
        allow 127.0.0.1;
        deny all;
    }
}

# Accès: curl http://localhost:8080/nginx_status
```

---

## 📚 Ressources

- [Nginx Documentation](https://nginx.org/en/docs/)
- [Nginx Config Generator](https://www.digitalocean.com/community/tools/nginx)
- [SSL Labs Test](https://www.ssllabs.com/ssltest/)
- [Let's Encrypt](https://letsencrypt.org/)
