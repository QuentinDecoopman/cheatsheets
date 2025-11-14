# Nginx

## Documentation

https://nginx.org/en/docs/

## Présentation

Nginx (prononcé "engine x") est un serveur web open source, créé par Igor Sysoev en 2004. Il est couramment utilisé
comme serveur web, reverse proxy, load balancer, cache HTTP et proxy pour mails.

## Installation (exemples)

- Debian/Ubuntu :

```sh
sudo apt update
sudo apt install nginx -y
```

- CentOS / RHEL :

```sh
sudo yum install epel-release -y
sudo yum install nginx -y
```

## Commandes utiles (systemd)

```sh
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl reload nginx   # recharger la configuration sans couper les connexions
sudo systemctl status nginx
sudo nginx -t                 # tester la configuration
```

## Fichiers de configuration importants

- Fichier principal : `/etc/nginx/nginx.conf`
- Répertoires courants : `/etc/nginx/conf.d/`, `/etc/nginx/sites-available/`, `/etc/nginx/sites-enabled/`
- Logs : `/var/log/nginx/access.log`, `/var/log/nginx/error.log`

## Concepts clés

- `server` : bloc qui définit un hôte virtuel (domaines, ports, SSL).
- `location` : règles pour traiter les chemins (statique, proxy, redirections, etc.).
- `upstream` : définition d'un pool de backends pour load balancing.

## Exemple : server block pour contenu statique

```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/example.com/html;
    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    access_log /var/log/nginx/example.access.log;
    error_log /var/log/nginx/example.error.log warn;
}
```

## Exemple : reverse proxy vers une app Node.js

```nginx
upstream app_backend {
    server 127.0.0.1:3000;
    # load balancing : server 127.0.0.1:3001; server 127.0.0.1:3002;
}

server {
    listen 80;
    server_name app.example.com;

    location / {
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_pass http://app_backend;
    }
}
```

## SSL / HTTPS (rapide)

Pour obtenir un certificat gratuit (Let's Encrypt) :

```sh
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d example.com -d www.example.com
```

Après installation, `certbot` mettra à jour un `server` pour rediriger vers HTTPS et gérer le renouvellement automatique.

## Bonnes pratiques et astuces

- Tester la configuration : `sudo nginx -t` avant de recharger.
- Utiliser `reload` plutôt que `restart` pour éviter de couper les connexions.
- Mettre en place `ufw` / firewall pour autoriser les ports 80/443 uniquement.
- Sur Debian/Ubuntu, préférer l'organisation `sites-available` / `sites-enabled` (liens symboliques).
- Activer gzip pour les ressources statiques si besoin.

## Ressources

- Documentation officielle : https://nginx.org/en/docs/
- Guide Nginx pour débutants : https://nginx.org/en/docs/beginners_guide.html

