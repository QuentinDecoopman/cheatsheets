# Conventions & Bonnes Pratiques Docker

## 📋 Conventions de Nommage

### Images

- **lowercase** avec tirets ou underscores
- Format: `organization/name:tag`

```bash
# ✅ Bon
myapp:latest
myapp:1.0.0
company/backend:v2.3.1
user/frontend:dev

# Tags sémantiques
myapp:1.0.0
myapp:latest
myapp:dev
myapp:prod
```

### Containers

- Noms descriptifs en **lowercase**

```bash
# ✅ Bon
docker run --name web-server nginx
docker run --name postgres-db postgres
docker run --name redis-cache redis

# ❌ Éviter
docker run --name container1 nginx
docker run --name temp nginx
```

### Volumes et Networks

```bash
# Volumes
docker volume create app-data
docker volume create postgres-data

# Networks
docker network create app-network
docker network create backend-network
```

---

## 🏗️ Dockerfile Best Practices

### Structure optimale

```dockerfile
# ✅ Bon Dockerfile

# 1. Image de base officielle et version spécifique
FROM node:18-alpine AS base

# 2. Métadonnées
LABEL maintainer="john@example.com"
LABEL version="1.0"
LABEL description="Application Node.js"

# 3. Variables d'environnement
ENV NODE_ENV=production
ENV PORT=3000

# 4. Répertoire de travail
WORKDIR /app

# 5. Copier package.json d'abord (cache layer)
COPY package*.json ./

# 6. Installer les dépendances
RUN npm ci --only=production

# 7. Copier le code source
COPY . .

# 8. Build si nécessaire
RUN npm run build

# 9. Exposer le port
EXPOSE 3000

# 10. Utilisateur non-root
USER node

# 11. Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node healthcheck.js

# 12. Commande de démarrage
CMD ["node", "dist/index.js"]
```

### Multi-stage builds

```dockerfile
# ✅ Utiliser multi-stage pour réduire la taille

# Stage 1: Build
FROM node:18-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# Stage 2: Production
FROM node:18-alpine AS production

WORKDIR /app

# Copier uniquement les fichiers nécessaires
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./

ENV NODE_ENV=production
EXPOSE 3000
USER node

CMD ["node", "dist/index.js"]
```

### Optimisation des layers

```dockerfile
# ❌ Mauvais - chaque RUN crée un layer
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y git
RUN apt-get clean

# ✅ Bon - combiner les commandes
RUN apt-get update && \
    apt-get install -y \
        curl \
        git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# ❌ Éviter
COPY . .
RUN npm install

# ✅ Bon - copier package.json d'abord
COPY package*.json ./
RUN npm ci
COPY . .
```

---

## ✅ Bonnes Pratiques Générales

### 1. Utiliser .dockerignore

```bash
# .dockerignore
node_modules
npm-debug.log
.git
.gitignore
.env
.env.local
*.md
.vscode
.idea
dist
build
coverage
*.log
.DS_Store
Dockerfile
docker-compose.yml
```

### 2. Versions spécifiques

```dockerfile
# ✅ Bon - version spécifique
FROM node:18.16.0-alpine
FROM python:3.11.4-slim

# ❌ Éviter - version latest (non reproductible)
FROM node:latest
FROM python
```

### 3. Images minimales

```dockerfile
# ✅ Préférer les images Alpine
FROM node:18-alpine     # ~170MB
FROM python:3.11-alpine # ~50MB

# Au lieu de
FROM node:18            # ~900MB
FROM python:3.11        # ~800MB
```

### 4. Utilisateur non-root

```dockerfile
# ✅ Créer et utiliser un utilisateur non-root
FROM node:18-alpine

# Créer un groupe et utilisateur
RUN addgroup -g 1001 -S appgroup && \
    adduser -S appuser -u 1001

WORKDIR /app

# Installer les dépendances en root
COPY package*.json ./
RUN npm ci --only=production

# Copier les fichiers
COPY --chown=appuser:appgroup . .

# Basculer vers l'utilisateur non-root
USER appuser

CMD ["node", "index.js"]
```

### 5. Health checks

```dockerfile
# Vérifier la santé du container
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

# Ou avec wget
HEALTHCHECK CMD wget --no-verbose --tries=1 --spider http://localhost:3000/health || exit 1

# Avec script Node.js
HEALTHCHECK CMD node healthcheck.js
```

---

## 🔄 Docker Compose

### Structure docker-compose.yml

```yaml
version: "3.8"

services:
  # Backend API
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
      target: production
    container_name: backend-api
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://user:pass@postgres:5432/mydb
      - REDIS_URL=redis://redis:6379
    env_file:
      - .env
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_started
    networks:
      - app-network
    volumes:
      - ./backend/logs:/app/logs
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 3s
      retries: 3

  # Frontend
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: frontend-app
    restart: unless-stopped
    ports:
      - "80:80"
    depends_on:
      - backend
    networks:
      - app-network

  # PostgreSQL
  postgres:
    image: postgres:15-alpine
    container_name: postgres-db
    restart: unless-stopped
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
    ports:
      - "5432:5432"
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d mydb"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Redis
  redis:
    image: redis:7-alpine
    container_name: redis-cache
    restart: unless-stopped
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    networks:
      - app-network
    command: redis-server --appendonly yes

  # Nginx (Reverse Proxy)
  nginx:
    image: nginx:alpine
    container_name: nginx-proxy
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/ssl:/etc/nginx/ssl:ro
    depends_on:
      - backend
      - frontend
    networks:
      - app-network

networks:
  app-network:
    driver: bridge

volumes:
  postgres-data:
  redis-data:
```

### docker-compose pour développement

```yaml
version: "3.8"

services:
  backend:
    build:
      context: ./backend
      target: development
    container_name: backend-dev
    ports:
      - "3000:3000"
      - "9229:9229" # Debug port
    environment:
      - NODE_ENV=development
    volumes:
      - ./backend:/app
      - /app/node_modules # Anonymous volume
    command: npm run dev

  frontend:
    build:
      context: ./frontend
      target: development
    container_name: frontend-dev
    ports:
      - "3001:3000"
    volumes:
      - ./frontend:/app
      - /app/node_modules
    command: npm start
```

---

## 🚀 Commandes Essentielles

### Build et Run

```bash
# Build une image
docker build -t myapp:1.0 .
docker build -t myapp:latest --target production .
docker build --no-cache -t myapp:1.0 .

# Run un container
docker run -d --name myapp -p 3000:3000 myapp:1.0
docker run -d --name myapp -p 3000:3000 --env-file .env myapp:1.0

# Run avec volume
docker run -d -v $(pwd)/data:/app/data myapp:1.0

# Run avec network
docker run -d --network app-network myapp:1.0

# Run interactif
docker run -it myapp:1.0 /bin/sh

# Run avec auto-suppression
docker run --rm myapp:1.0
```

### Gestion des containers

```bash
# Lister
docker ps           # Actifs
docker ps -a        # Tous

# Stop/Start/Restart
docker stop myapp
docker start myapp
docker restart myapp

# Logs
docker logs myapp
docker logs -f myapp           # Follow
docker logs --tail 100 myapp   # 100 dernières lignes

# Exec dans un container
docker exec -it myapp /bin/sh
docker exec myapp ls /app

# Statistiques
docker stats
docker stats myapp

# Inspecter
docker inspect myapp

# Supprimer
docker rm myapp
docker rm -f myapp  # Force
docker rm $(docker ps -aq)  # Tous les containers arrêtés
```

### Gestion des images

```bash
# Lister
docker images
docker images -a

# Pull/Push
docker pull nginx:alpine
docker push myregistry.com/myapp:1.0

# Tag
docker tag myapp:1.0 myapp:latest
docker tag myapp:1.0 myregistry.com/myapp:1.0

# Supprimer
docker rmi myapp:1.0
docker image prune  # Images inutilisées
docker image prune -a  # Toutes les images non utilisées

# History
docker history myapp:1.0

# Save/Load
docker save myapp:1.0 > myapp.tar
docker load < myapp.tar
```

### Docker Compose

```bash
# Build et démarrer
docker-compose up
docker-compose up -d  # Détaché
docker-compose up --build  # Rebuild

# Arrêter
docker-compose stop
docker-compose down  # Arrête et supprime
docker-compose down -v  # Inclut les volumes

# Logs
docker-compose logs
docker-compose logs -f backend

# Exec
docker-compose exec backend /bin/sh

# Scale
docker-compose up -d --scale backend=3

# Build uniquement
docker-compose build
docker-compose build --no-cache
```

### Volumes

```bash
# Créer
docker volume create mydata

# Lister
docker volume ls

# Inspecter
docker volume inspect mydata

# Supprimer
docker volume rm mydata
docker volume prune  # Volumes inutilisés
```

### Networks

```bash
# Créer
docker network create app-network
docker network create --driver bridge app-network

# Lister
docker network ls

# Inspecter
docker network inspect app-network

# Connecter/Déconnecter
docker network connect app-network myapp
docker network disconnect app-network myapp

# Supprimer
docker network rm app-network
docker network prune
```

---

## 🔒 Sécurité

### 1. Scan des vulnérabilités

```bash
# Docker Scout (intégré)
docker scout cves myapp:1.0

# Trivy
trivy image myapp:1.0

# Snyk
snyk container test myapp:1.0
```

### 2. Limiter les ressources

```yaml
# docker-compose.yml
services:
  backend:
    deploy:
      resources:
        limits:
          cpus: "0.5"
          memory: 512M
        reservations:
          cpus: "0.25"
          memory: 256M
```

```bash
# CLI
docker run -d --cpus=".5" --memory="512m" myapp:1.0
```

### 3. Read-only filesystem

```dockerfile
# Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm ci --only=production
USER node
CMD ["node", "index.js"]
```

```bash
# Run
docker run -d --read-only --tmpfs /tmp myapp:1.0
```

### 4. Secrets

```bash
# Créer un secret (Docker Swarm)
echo "my_secret_password" | docker secret create db_password -

# Dans docker-compose.yml
version: '3.8'
services:
  db:
    image: postgres
    secrets:
      - db_password
secrets:
  db_password:
    external: true
```

---

## 🛠️ Optimisation

### Réduire la taille des images

```dockerfile
# 1. Multi-stage builds
# 2. Images Alpine
# 3. Combiner les RUN
# 4. Nettoyer les caches

FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package.json ./
USER node
CMD ["node", "dist/index.js"]
```

### Utiliser le cache efficacement

```dockerfile
# ✅ Bon - dependencies changent moins souvent
COPY package*.json ./
RUN npm ci
COPY . .

# ❌ Mauvais - invalide le cache à chaque changement
COPY . .
RUN npm ci
```

---

## 📚 Ressources

- [Docker Documentation](https://docs.docker.com/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Dockerfile Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [Docker Hub](https://hub.docker.com/)
- [Play with Docker](https://labs.play-with-docker.com/)
