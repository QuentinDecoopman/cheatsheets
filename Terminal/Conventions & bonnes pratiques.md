# Conventions & Bonnes Pratiques Terminal

## 📋 Commandes de Base

### Navigation

```bash
# Afficher le répertoire courant
pwd

# Lister les fichiers
ls
ls -l      # Format détaillé
ls -la     # Inclut les fichiers cachés
ls -lh     # Tailles lisibles (human-readable)
ls -ltr    # Tri par date de modification

# Changer de répertoire
cd /path/to/directory
cd ~       # Répertoire home
cd -       # Répertoire précédent
cd ..      # Répertoire parent
cd ../..   # Deux niveaux au-dessus

# Créer un répertoire
mkdir directory_name
mkdir -p path/to/nested/directory  # Créer parents si nécessaire

# Supprimer
rm file.txt
rm -r directory/     # Récursif pour dossiers
rm -rf directory/    # Force (pas de confirmation)
rmdir directory/     # Seulement si vide
```

### Manipulation de Fichiers

```bash
# Copier
cp source.txt destination.txt
cp -r source_dir/ dest_dir/  # Récursif
cp -i file.txt dest/         # Interactif (demande confirmation)

# Déplacer/Renommer
mv old_name.txt new_name.txt
mv file.txt /path/to/directory/
mv -i source dest  # Interactif

# Créer fichier vide
touch file.txt

# Afficher contenu
cat file.txt
less file.txt      # Navigation avec q pour quitter
head file.txt      # 10 premières lignes
head -n 20 file.txt  # 20 premières lignes
tail file.txt      # 10 dernières lignes
tail -f file.txt   # Suivi en temps réel
```

---

## 🔍 Recherche

### find

```bash
# Trouver par nom
find /path -name "*.txt"
find . -name "file.txt"
find . -iname "FILE.TXT"  # Insensible à la casse

# Trouver par type
find . -type f  # Fichiers
find . -type d  # Dossiers
find . -type l  # Liens symboliques

# Trouver par taille
find . -size +100M  # Plus de 100MB
find . -size -1k    # Moins de 1KB

# Trouver par date
find . -mtime -7    # Modifié dans les 7 derniers jours
find . -mtime +30   # Modifié il y a plus de 30 jours

# Exécuter une commande sur les résultats
find . -name "*.log" -delete
find . -name "*.txt" -exec cat {} \;
find . -type f -exec chmod 644 {} \;

# Combinaisons
find . -type f -name "*.js" -size +1M -mtime -30
```

### grep

```bash
# Recherche dans fichiers
grep "pattern" file.txt
grep -i "pattern" file.txt      # Insensible à la casse
grep -r "pattern" directory/    # Récursif
grep -n "pattern" file.txt      # Avec numéros de ligne
grep -v "pattern" file.txt      # Inverse (lignes ne contenant PAS)
grep -c "pattern" file.txt      # Compte les occurrences

# Expressions régulières
grep -E "pattern1|pattern2" file.txt
grep -E "^start" file.txt       # Commence par
grep -E "end$" file.txt         # Finit par

# Contexte
grep -A 3 "pattern" file.txt    # 3 lignes après
grep -B 3 "pattern" file.txt    # 3 lignes avant
grep -C 3 "pattern" file.txt    # 3 lignes avant et après
```

---

## 📝 Éditeurs de Texte

### nano (simple)

```bash
nano file.txt

# Raccourcis dans nano:
# Ctrl+O : Sauvegarder
# Ctrl+X : Quitter
# Ctrl+K : Couper ligne
# Ctrl+U : Coller
# Ctrl+W : Rechercher
```

### vim (avancé)

```bash
vim file.txt

# Modes vim:
# Esc : Mode commande
# i : Mode insertion
# v : Mode visuel

# Commandes:
# :w : Sauvegarder
# :q : Quitter
# :wq : Sauvegarder et quitter
# :q! : Quitter sans sauvegarder
# /pattern : Rechercher
# dd : Supprimer ligne
# yy : Copier ligne
# p : Coller
```

---

## 🔗 Pipes et Redirections

### Redirections

```bash
# > : Écrire (écrase)
echo "Hello" > file.txt

# >> : Ajouter (append)
echo "World" >> file.txt

# < : Entrée depuis fichier
sort < unsorted.txt

# 2> : Rediriger erreurs
command 2> errors.log

# &> : Rediriger tout (stdout + stderr)
command &> output.log

# /dev/null : Jeter la sortie
command > /dev/null 2>&1
```

### Pipes

```bash
# | : Envoyer la sortie d'une commande à une autre
ls -l | grep ".txt"
cat file.txt | grep "pattern" | wc -l

# Exemples pratiques
ps aux | grep node
history | grep git
cat log.txt | grep ERROR | tail -20

# Chaînes complexes
cat access.log | grep "404" | awk '{print $1}' | sort | uniq -c | sort -nr
```

---

## 💾 Archivage et Compression

### tar

```bash
# Créer archive
tar -cvf archive.tar directory/
tar -czvf archive.tar.gz directory/  # Avec gzip
tar -cjvf archive.tar.bz2 directory/ # Avec bzip2

# Extraire
tar -xvf archive.tar
tar -xzvf archive.tar.gz
tar -xjvf archive.tar.bz2

# Lister contenu
tar -tvf archive.tar

# Options:
# c : create
# x : extract
# v : verbose
# f : file
# z : gzip
# j : bzip2
```

### zip/unzip

```bash
# Créer zip
zip archive.zip file1.txt file2.txt
zip -r archive.zip directory/

# Extraire
unzip archive.zip
unzip archive.zip -d /path/to/directory/

# Lister contenu
unzip -l archive.zip
```

---

## 🔐 Permissions

### chmod (changer permissions)

```bash
# Notation symbolique
chmod u+x file.sh     # Ajouter exécution pour user
chmod g+w file.txt    # Ajouter écriture pour group
chmod o-r file.txt    # Retirer lecture pour others
chmod a+x script.sh   # Ajouter exécution pour all

# Notation octale
chmod 644 file.txt    # rw-r--r--
chmod 755 script.sh   # rwxr-xr-x
chmod 600 secret.txt  # rw-------
chmod 777 public/     # rwxrwxrwx (déconseillé!)

# Récursif
chmod -R 755 directory/

# Référence:
# 4 = read (r)
# 2 = write (w)
# 1 = execute (x)
```

### chown (changer propriétaire)

```bash
# Changer propriétaire
sudo chown user file.txt

# Changer propriétaire et groupe
sudo chown user:group file.txt

# Récursif
sudo chown -R user:group directory/

# Seulement le groupe
sudo chgrp group file.txt
```

---

## 📊 Processus et Système

### ps (processus)

```bash
ps                # Processus de la session courante
ps aux            # Tous les processus
ps -ef            # Format complet
ps aux | grep node  # Filtrer processus

# Colonnes importantes:
# PID : Process ID
# %CPU : Utilisation CPU
# %MEM : Utilisation mémoire
# COMMAND : Commande
```

### top/htop

```bash
top               # Moniteur interactif
htop              # Version améliorée (installer avec apt/brew)

# Dans top:
# q : Quitter
# k : Tuer un processus
# M : Trier par mémoire
# P : Trier par CPU
```

### kill

```bash
# Tuer processus par PID
kill 1234
kill -9 1234      # Force (SIGKILL)
kill -15 1234     # Graceful (SIGTERM)

# Tuer par nom
killall node
pkill -f "node server.js"
```

### Informations système

```bash
# Utilisation disque
df -h             # Espaces disque
du -sh directory/ # Taille dossier
du -h --max-depth=1  # Tailles sous-dossiers

# Mémoire
free -h

# Uptime
uptime

# Informations CPU
lscpu

# Informations réseau
ifconfig
ip addr show
```

---

## 🌐 Réseau

### curl

```bash
# GET request
curl https://api.example.com

# POST request
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"name":"John"}'

# Avec authentication
curl -u username:password https://api.example.com

# Sauvegarder dans fichier
curl -o output.html https://example.com
curl -O https://example.com/file.zip  # Garde le nom

# Suivre redirections
curl -L https://example.com

# Headers
curl -I https://example.com
curl -v https://example.com  # Verbose
```

### wget

```bash
# Télécharger fichier
wget https://example.com/file.zip

# En arrière-plan
wget -b https://example.com/large-file.zip

# Continuer téléchargement interrompu
wget -c https://example.com/file.zip

# Télécharger site entier
wget -r -np -k https://example.com
```

### ping/traceroute

```bash
# Tester connectivité
ping google.com
ping -c 4 google.com  # 4 paquets seulement

# Tracer route
traceroute google.com

# Port ouvert
telnet example.com 80
nc -zv example.com 80
```

---

## ✅ Bonnes Pratiques

### 1. Alias utiles

```bash
# .bashrc ou .zshrc
alias ll='ls -alh'
alias la='ls -A'
alias l='ls -CF'
alias ..='cd ..'
alias ...='cd ../..'
alias gs='git status'
alias ga='git add'
alias gc='git commit'
alias gp='git push'
alias gpl='git pull'
alias k='kubectl'
alias d='docker'
```

### 2. Historique de commandes

```bash
# Rechercher dans l'historique
history | grep git

# Ctrl+R : Recherche interactive dans l'historique

# Réexécuter commande précédente
!!

# Réexécuter commande #123
!123

# Dernier argument de la commande précédente
!$
```

### 3. Raccourcis clavier

```bash
# Navigation
Ctrl+A : Début de ligne
Ctrl+E : Fin de ligne
Ctrl+U : Effacer jusqu'au début
Ctrl+K : Effacer jusqu'à la fin
Ctrl+W : Effacer mot précédent
Ctrl+L : Effacer écran (ou clear)

# Processus
Ctrl+C : Interrompre
Ctrl+Z : Suspendre
Ctrl+D : EOF / Logout

# Recherche
Ctrl+R : Recherche historique
```

### 4. Scripts Bash

```bash
#!/bin/bash

# Variables
NAME="John"
AGE=30

# Conditions
if [ "$AGE" -gt 18 ]; then
  echo "Adult"
fi

# Boucles
for file in *.txt; do
  echo "Processing $file"
done

# Fonctions
function greet() {
  echo "Hello $1"
}

greet "World"

# Arguments
echo "Script: $0"
echo "Arg 1: $1"
echo "All args: $@"
echo "Number of args: $#"
```

### 5. Sécurité

```bash
# ✅ Bon - guillemets pour variables
rm "$filename"

# ❌ Dangereux
rm $filename  # Problème si espaces

# ✅ Vérifier avant suppression
rm -i file.txt

# ❌ Jamais
sudo rm -rf /  # CATASTROPHIQUE!
```

---

## 📚 Ressources

- [Bash Guide](https://mywiki.wooledge.org/BashGuide)
- [ExplainShell](https://explainshell.com/)
- [Command Line Cheat Sheet](https://www.git-tower.com/blog/command-line-cheat-sheet/)
- [Linux Command](https://linuxcommand.org/)
