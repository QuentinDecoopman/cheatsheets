# SSH

## Créer une clé

    ssh-keygen -t ed25519 -f ~/.ssh/mon_nom_de_clef

la clé est créée dans le dossier .ssh.

## Copier la clé & l'envoyer aux serveur

    ssh-copy-id -i ~/.ssh/mon_nom_de_clef.pub user@serveur

La clé est envoyée au serveur
