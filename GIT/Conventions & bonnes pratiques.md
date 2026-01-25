# Conventions & Bonnes Pratiques Git

## 📋 Conventions de Nommage

### Branches

- **kebab-case** pour les noms de branches
- Préfixer selon le type de travail

```bash
# Features
feature/user-authentication
feature/payment-integration

# Bugfixes
bugfix/login-error
fix/header-responsive

# Hotfixes
hotfix/critical-security-patch

# Releases
release/v1.2.0

# Branches principales
main (ou master)
develop
staging
```

### Commits

- Messages clairs et descriptifs
- Convention Conventional Commits

```bash
# Format : <type>(<scope>): <subject>

# Types courants :
feat: nouvelle fonctionnalité
fix: correction de bug
docs: documentation
style: formatage (pas de changement de code)
refactor: refactoring
test: ajout/modification de tests
chore: tâches de maintenance
perf: amélioration de performance

# Exemples
feat(auth): add JWT authentication
fix(api): resolve CORS issue
docs(readme): update installation instructions
refactor(user): simplify user service logic
test(auth): add unit tests for login
```

---

## ✅ Bonnes Pratiques

### 1. Commits atomiques et fréquents

```bash
# ✅ Bon - commits petits et fréquents
git add src/components/Button.js
git commit -m "feat(button): add primary button variant"

git add src/components/Button.test.js
git commit -m "test(button): add tests for button component"

# ❌ Éviter les gros commits
git add .
git commit -m "update stuff"
```

### 2. Messages de commit descriptifs

```bash
# ✅ Bon
git commit -m "fix(auth): prevent duplicate login requests

- Add debounce to login button
- Cache authentication token
- Fixes #123"

# ❌ Éviter
git commit -m "fix bug"
git commit -m "wip"
git commit -m "update"
```

### 3. Ne jamais commit de secrets

```bash
# ✅ Utiliser .gitignore
echo ".env" >> .gitignore
echo "node_modules/" >> .gitignore
echo "*.log" >> .gitignore

# Si déjà commité par erreur
git rm --cached .env
git commit -m "chore: remove .env from repository"

# Nettoyer l'historique (dangereux)
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch .env" \
  --prune-empty --tag-name-filter cat -- --all
```

### 4. Pull avant de Push

```bash
# ✅ Bon workflow
git pull origin main
# Résoudre les conflits si nécessaire
git push origin main

# Avec rebase pour historique linéaire
git pull --rebase origin main
git push origin main
```

---

## 🌿 Workflow Git

### Git Flow

```bash
# 1. Créer une branche feature
git checkout -b feature/new-feature develop

# 2. Travailler et commiter
git add .
git commit -m "feat: implement new feature"

# 3. Mettre à jour avec develop
git checkout develop
git pull origin develop
git checkout feature/new-feature
git rebase develop

# 4. Merger dans develop
git checkout develop
git merge --no-ff feature/new-feature
git push origin develop

# 5. Supprimer la branche
git branch -d feature/new-feature
git push origin --delete feature/new-feature

# Release
git checkout -b release/v1.2.0 develop
# Préparer la release
git checkout main
git merge --no-ff release/v1.2.0
git tag -a v1.2.0 -m "Release version 1.2.0"
git push origin main --tags

# Hotfix
git checkout -b hotfix/critical-fix main
# Corriger le bug
git checkout main
git merge --no-ff hotfix/critical-fix
git tag -a v1.2.1 -m "Hotfix 1.2.1"
git checkout develop
git merge --no-ff hotfix/critical-fix
```

### GitHub Flow (simplifié)

```bash
# 1. Créer une branche depuis main
git checkout -b feature/my-feature main

# 2. Commits réguliers
git add .
git commit -m "feat: add feature"

# 3. Push et créer Pull Request
git push origin feature/my-feature

# 4. Review et merge via GitHub

# 5. Mettre à jour local
git checkout main
git pull origin main
git branch -d feature/my-feature
```

---

## 🔄 Commandes Essentielles

### Initialisation et Configuration

```bash
# Initialiser un dépôt
git init

# Cloner un dépôt
git clone https://github.com/user/repo.git
git clone --depth 1 https://github.com/user/repo.git  # Shallow clone

# Configuration
git config --global user.name "John Doe"
git config --global user.email "john@example.com"
git config --global core.editor "code --wait"
git config --global init.defaultBranch main

# Voir la configuration
git config --list
```

### Staging et Commits

```bash
# Ajouter des fichiers
git add file.txt
git add .
git add -A  # Tout
git add -p  # Interactif (sélectionner les hunks)

# Status
git status
git status -s  # Court

# Commit
git commit -m "message"
git commit -am "message"  # Add + commit (fichiers tracked)
git commit --amend  # Modifier le dernier commit
git commit --amend --no-edit  # Sans changer le message

# Unstage
git reset HEAD file.txt
git restore --staged file.txt  # Nouvelle syntaxe

# Annuler les modifications
git checkout -- file.txt
git restore file.txt  # Nouvelle syntaxe
```

### Branches

```bash
# Lister
git branch
git branch -a  # Toutes (locales et distantes)
git branch -r  # Distantes

# Créer
git branch feature/new-feature
git checkout -b feature/new-feature  # Créer et basculer
git switch -c feature/new-feature  # Nouvelle syntaxe

# Basculer
git checkout feature/new-feature
git switch feature/new-feature  # Nouvelle syntaxe

# Supprimer
git branch -d feature/new-feature  # Suppression sûre
git branch -D feature/new-feature  # Force
git push origin --delete feature/new-feature  # Distante

# Renommer
git branch -m old-name new-name
```

### Merge et Rebase

```bash
# Merge
git checkout main
git merge feature/new-feature
git merge --no-ff feature/new-feature  # Sans fast-forward
git merge --squash feature/new-feature  # Squash tous les commits

# Rebase
git checkout feature/new-feature
git rebase main

# Rebase interactif
git rebase -i HEAD~3  # 3 derniers commits
# pick, reword, edit, squash, fixup, drop

# Continuer/annuler
git rebase --continue
git rebase --abort

# Cherry-pick
git cherry-pick <commit-hash>
```

### Remote

```bash
# Lister
git remote -v

# Ajouter
git remote add origin https://github.com/user/repo.git

# Modifier
git remote set-url origin https://github.com/user/new-repo.git

# Supprimer
git remote remove origin

# Fetch
git fetch origin
git fetch --all

# Pull
git pull origin main
git pull --rebase origin main

# Push
git push origin main
git push -u origin main  # Définir upstream
git push --force-with-lease  # Force sûr
git push --tags  # Push les tags
```

### Historique et Logs

```bash
# Historique
git log
git log --oneline
git log --graph --oneline --all
git log --stat
git log -p  # Avec diff
git log --since="2 weeks ago"
git log --author="John"

# Format personnalisé
git log --pretty=format:"%h - %an, %ar : %s"

# Rechercher dans l'historique
git log -S "function_name"
git log --grep="feat"

# Voir les changements
git show <commit-hash>
git diff
git diff HEAD~1 HEAD  # Dernier commit
git diff main feature/branch
git diff --staged  # Fichiers staged
```

### Stash

```bash
# Mettre de côté
git stash
git stash save "work in progress"
git stash -u  # Inclure les fichiers untracked

# Lister
git stash list

# Appliquer
git stash apply  # Dernier stash
git stash apply stash@{1}  # Stash spécifique
git stash pop  # Apply + drop

# Supprimer
git stash drop stash@{0}
git stash clear  # Tout
```

### Reset et Revert

```bash
# Reset (modifie l'historique)
git reset --soft HEAD~1   # Garde les changements staged
git reset --mixed HEAD~1  # Garde les changements unstaged (défaut)
git reset --hard HEAD~1   # Supprime les changements (dangereux)

# Revert (crée un nouveau commit)
git revert <commit-hash>  # Annule un commit spécifique
git revert HEAD           # Annule le dernier commit

# Clean (supprimer fichiers untracked)
git clean -n  # Dry run
git clean -f  # Force
git clean -fd  # Inclure les dossiers
```

### Tags

```bash
# Créer
git tag v1.0.0
git tag -a v1.0.0 -m "Version 1.0.0"  # Annoté

# Lister
git tag
git tag -l "v1.*"

# Push
git push origin v1.0.0
git push origin --tags

# Supprimer
git tag -d v1.0.0  # Local
git push origin --delete v1.0.0  # Distant

# Checkout un tag
git checkout v1.0.0
```

---

## 🔍 Résolution de Conflits

### Workflow de résolution

```bash
# 1. Identifier les conflits
git status

# 2. Ouvrir les fichiers en conflit
# Chercher les marqueurs :
<<<<<<< HEAD
Code actuel
=======
Code entrant
>>>>>>> feature/branch

# 3. Résoudre manuellement

# 4. Marquer comme résolu
git add file.txt

# 5. Continuer
git commit  # Pour merge
git rebase --continue  # Pour rebase

# Outils de merge
git mergetool
```

---

## 🛠️ Outils et Commandes Avancées

### Bisect (trouver le commit qui a introduit un bug)

```bash
git bisect start
git bisect bad  # Commit actuel est mauvais
git bisect good <commit-hash>  # Commit qui fonctionnait

# Git teste automatiquement
# Marquer chaque test
git bisect good  # ou
git bisect bad

# Terminer
git bisect reset
```

### Reflog (historique des références)

```bash
# Voir l'historique des HEAD
git reflog

# Récupérer un commit "perdu"
git checkout <commit-hash>
git cherry-pick <commit-hash>
```

### Blame (qui a modifié quoi)

```bash
git blame file.txt
git blame -L 10,20 file.txt  # Lignes 10 à 20
```

### Submodules

```bash
# Ajouter
git submodule add https://github.com/user/repo.git path/to/submodule

# Cloner avec submodules
git clone --recursive https://github.com/user/repo.git

# Initialiser et mettre à jour
git submodule init
git submodule update

# Tout en une commande
git submodule update --init --recursive

# Mettre à jour tous les submodules
git submodule update --remote
```

---

## 🔒 Sécurité et Bonnes Pratiques

### .gitignore

```bash
# Fichiers système
.DS_Store
Thumbs.db

# IDE
.vscode/
.idea/
*.swp

# Dependencies
node_modules/
vendor/
venv/

# Build
dist/
build/
*.log

# Environnement
.env
.env.local
*.key
*.pem

# Utiliser des templates
# https://github.com/github/gitignore
```

### Hooks Git

```bash
# Dans .git/hooks/

# pre-commit (avant chaque commit)
#!/bin/sh
npm test
npm run lint

# pre-push (avant chaque push)
#!/bin/sh
npm run test:integration

# commit-msg (valider le message)
#!/bin/sh
commit_msg=$(cat "$1")
if ! echo "$commit_msg" | grep -qE "^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .+"; then
    echo "Invalid commit message format"
    exit 1
fi

# Rendre exécutable
chmod +x .git/hooks/pre-commit
```

### GPG Signing

```bash
# Configurer
git config --global user.signingkey <GPG-KEY-ID>
git config --global commit.gpgsign true

# Signer un commit
git commit -S -m "feat: add feature"

# Vérifier
git log --show-signature
```

---

## 📝 Alias Utiles

```bash
# Configuration des alias
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

# Utilisation
git co main
git br -a
git lg
```

---

## 📚 Ressources

- [Git Documentation](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book/fr/v2)
- [Learn Git Branching](https://learngitbranching.js.org/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [GitHub Guides](https://guides.github.com/)
