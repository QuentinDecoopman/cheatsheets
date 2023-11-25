# Cheatsheet Git

- [Index](/Readme.md)

- [Cheatsheet PDF](https://training.github.com/downloads/fr/github-git-cheat-sheet.pdf)
- [Cheatsheet](https://training.github.com/downloads/fr/github-git-cheat-sheet/)

## Commandes de base :

### Initialiser un nouveau dépôt Git :

     git init

### Cloner un dépôt :

    git clone <url_du_dépôt>

### Vérifier l'état de votre dépôt :

    git status

### Ajouter des modifications à la zone de transit :

    git add <fichier(s)>

### Valider les modifications :

    git commit -m "Votre message de validation"

### Pousser les modifications vers un dépôt distant :

    git push <distant> <branche>

## Branche et Fusion :

### Créer une nouvelle branche :

    git branch <nom_de_branche>

### Changer de branche :

    git checkout <nom_de_branche>

(Ou utiliser la commande combinée :

    git checkout -b <nom_de_branche>

pour créer et passer à une branche en une seule étape.)

### Fusionner les modifications d'une branche dans une autre :

    git merge <nom_de_branche>

## Dépôts distants :

### Ajouter un dépôt distant :

    git remote add <nom_distant> <url_du_dépôt distant>

### Récupérer les modifications d'un dépôt distant :

    git fetch <nom_distant>

### Récupérer les modifications d'un dépôt distant et les fusionner :

    git pull <nom_distant> <nom_branche>

## Annulation des modifications :

### Annuler les modifications dans le répertoire de travail :

    git checkout -- <fichier(s)>

### Annuler la dernière validation (en conservant les modifications) :

    git reset --soft HEAD^

### Annuler la dernière validation et abandonner les modifications :

    git reset --hard HEAD^

## Journalisation et Historique :

### Afficher l'historique des validations :

    git log

### Afficher les modifications dans une validation :

    git show <hash_de_la_validation>

## Divers :

## Configurer les informations utilisateur Git :

    git config --global user.name "Votre Nom"
    git config --global user.email "votre.email@exemple.com"
