# Conventions & Bonnes Pratiques SSH

## 📋 Commandes de Base

### Connexion

```bash
# Connexion simple
ssh user@hostname

# Connexion avec port spécifique
ssh user@hostname -p 2222

# Connexion avec clé privée
ssh -i ~/.ssh/id_rsa user@hostname

# Connexion verbeux (debug)
ssh -v user@hostname
ssh -vv user@hostname  # Plus de détails
ssh -vvv user@hostname # Maximum de détails
```

### Transfert de fichiers (SCP)

```bash
# Copier vers serveur distant
scp file.txt user@hostname:/remote/path/

# Copier depuis serveur distant
scp user@hostname:/remote/path/file.txt /local/path/

# Copier dossier récursivement
scp -r folder/ user@hostname:/remote/path/

# Avec port spécifique
scp -P 2222 file.txt user@hostname:/path/

# Avec clé privée
scp -i ~/.ssh/id_rsa file.txt user@hostname:/path/
```

### SFTP

```bash
# Connexion SFTP
sftp user@hostname

# Commandes SFTP
put file.txt          # Upload
get file.txt          # Download
ls                    # Liste fichiers distants
lls                   # Liste fichiers locaux
pwd                   # Dossier distant actuel
lpwd                  # Dossier local actuel
cd /path              # Changer dossier distant
lcd /path             # Changer dossier local
mkdir dirname         # Créer dossier distant
rm file.txt           # Supprimer fichier distant
exit                  # Quitter
```

---

## 🔐 Authentification par Clés SSH

### Générer une paire de clés

```bash
# RSA (recommandé 4096 bits)
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# Ed25519 (plus moderne, plus sécurisé)
ssh-keygen -t ed25519 -C "your_email@example.com"

# Spécifier le fichier
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_myserver

# Avec passphrase (recommandé)
ssh-keygen -t ed25519 -C "your_email@example.com"
# Enter passphrase when prompted
```

### Copier la clé publique sur le serveur

```bash
# Méthode automatique
ssh-copy-id user@hostname

# Avec port spécifique
ssh-copy-id -p 2222 user@hostname

# Avec clé spécifique
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@hostname

# Méthode manuelle
cat ~/.ssh/id_rsa.pub | ssh user@hostname "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

# Ou copier-coller manuellement
# 1. Afficher la clé publique
cat ~/.ssh/id_rsa.pub

# 2. Sur le serveur
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
# Coller la clé, sauvegarder

# 3. Permissions correctes
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

## ⚙️ Configuration SSH Client

### ~/.ssh/config

```bash
# Configuration par défaut
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3

# Serveur de production
Host prod
    HostName 192.168.1.100
    User deploy
    Port 22
    IdentityFile ~/.ssh/id_rsa_prod
    ForwardAgent yes

# Serveur de développement
Host dev
    HostName dev.example.com
    User developer
    IdentityFile ~/.ssh/id_ed25519_dev

# Bastion (jump host)
Host bastion
    HostName bastion.example.com
    User admin
    IdentityFile ~/.ssh/id_rsa

Host internal-server
    HostName 10.0.0.10
    User admin
    ProxyJump bastion

# Tunnel SSH
Host tunnel
    HostName tunnel.example.com
    User user
    LocalForward 3306 localhost:3306
    LocalForward 5432 localhost:5432

# Utilisation
ssh prod        # Se connecte à 192.168.1.100 avec user deploy
ssh dev         # Se connecte à dev.example.com
ssh internal-server  # Se connecte via bastion
```

---

## 🔒 Sécurisation du Serveur SSH

### /etc/ssh/sshd_config

```bash
# Port SSH (changer le port par défaut)
Port 2222

# Protocole
Protocol 2

# Désactiver connexion root
PermitRootLogin no

# Authentification par clé uniquement
PubkeyAuthentication yes
PasswordAuthentication no
PermitEmptyPasswords no

# Challenge response
ChallengeResponseAuthentication no

# PAM
UsePAM yes

# X11 Forwarding
X11Forwarding no

# Max tentatives
MaxAuthTries 3

# Timeout
ClientAliveInterval 300
ClientAliveCountMax 2

# Limiter les utilisateurs
AllowUsers user1 user2
# Ou groupes
AllowGroups sshusers

# Banner
Banner /etc/ssh/banner.txt

# Logging
SyslogFacility AUTH
LogLevel VERBOSE

# Après modification, redémarrer SSH
sudo systemctl restart sshd
# Tester avant de fermer la session!
```

---

## 🔑 SSH Agent

### Utilisation de ssh-agent

```bash
# Démarrer ssh-agent
eval $(ssh-agent)

# Ajouter clé
ssh-add ~/.ssh/id_rsa

# Ajouter avec passphrase (stockée temporairement)
ssh-add -t 3600 ~/.ssh/id_rsa  # Expire après 1 heure

# Lister les clés
ssh-add -l

# Supprimer toutes les clés
ssh-add -D

# Agent forwarding (utiliser clés locales sur serveur distant)
ssh -A user@hostname
# Ou dans config:
# ForwardAgent yes
```

---

## 🌐 Tunneling SSH

### Local Port Forwarding

```bash
# Accéder à un service distant via localhost
ssh -L local_port:remote_host:remote_port user@ssh_server

# Exemple: accéder à MySQL distant sur localhost:3306
ssh -L 3306:localhost:3306 user@database-server

# Accéder à un service dans un réseau privé
ssh -L 8080:internal-server:80 user@bastion
# http://localhost:8080 → internal-server:80
```

### Remote Port Forwarding

```bash
# Exposer un service local sur le serveur distant
ssh -R remote_port:localhost:local_port user@remote_server

# Exemple: exposer serveur web local sur le serveur distant
ssh -R 8080:localhost:3000 user@remote-server
# remote-server:8080 → localhost:3000
```

### Dynamic Port Forwarding (SOCKS Proxy)

```bash
# Créer un proxy SOCKS
ssh -D 9090 user@remote-server

# Configurer navigateur pour utiliser SOCKS proxy localhost:9090
# Tout le traffic passe par le serveur SSH
```

### Maintenir le tunnel ouvert

```bash
# Avec autossh
sudo apt install autossh

autossh -M 0 -N -L 3306:localhost:3306 user@server

# Ou avec options
autossh -M 0 -o "ServerAliveInterval 30" -o "ServerAliveCountMax 3" -N -L 3306:localhost:3306 user@server
```

---

## 📁 Rsync via SSH

```bash
# Synchroniser dossiers
rsync -avz -e ssh /local/path/ user@hostname:/remote/path/

# Options:
# -a archive mode (recursive, preserve permissions, etc.)
# -v verbose
# -z compression
# -e ssh utiliser SSH

# Dry run (voir ce qui serait fait)
rsync -avzn -e ssh /local/path/ user@hostname:/remote/path/

# Exclure fichiers
rsync -avz --exclude='node_modules' --exclude='.git' -e ssh /local/path/ user@hostname:/remote/path/

# Supprimer fichiers qui n'existent plus localement
rsync -avz --delete -e ssh /local/path/ user@hostname:/remote/path/

# Avec progress
rsync -avz --progress -e ssh /local/path/ user@hostname:/remote/path/

# Port spécifique
rsync -avz -e "ssh -p 2222" /local/path/ user@hostname:/remote/path/
```

---

## 🔍 Debug et Troubleshooting

```bash
# Connexion verbeux
ssh -vvv user@hostname

# Vérifier permissions
# Sur le serveur
ls -la ~/.ssh/
# authorized_keys doit être 600
# .ssh doit être 700

# Tester connexion sans exécution de commande
ssh -T git@github.com

# Logs SSH serveur
sudo tail -f /var/log/auth.log  # Ubuntu/Debian
sudo tail -f /var/log/secure    # CentOS/RHEL

# Tester config SSH serveur
sudo sshd -t

# Vérifier clés ajoutées à ssh-agent
ssh-add -l

# Permissions correctes
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/config
```

---

## 🛡️ Fail2ban pour SSH

```bash
# Installation
sudo apt install fail2ban

# Configuration
sudo nano /etc/fail2ban/jail.local

[sshd]
enabled = true
port = 22
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
findtime = 600

# Redémarrer
sudo systemctl restart fail2ban

# Status
sudo fail2ban-client status sshd

# Débannir une IP
sudo fail2ban-client set sshd unbanip 192.168.1.1
```

---

## 📚 Ressources

- [OpenSSH Documentation](https://www.openssh.com/manual.html)
- [SSH.com Guide](https://www.ssh.com/academy/ssh)
- [DigitalOcean SSH Tutorial](https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys)
