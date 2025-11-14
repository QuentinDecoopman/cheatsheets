# Comment sécuriser les données

**Le hachage**

Le hachage est une technique qui permet de transformer une donnée (texte, fichier, etc.) en une chaîne de caractères unique et de taille fixe appelée "hash". Le hachage est irréversible, c'est-à-dire qu'il est impossible de retrouver la donnée originale à partir du hash.

**Cas d'utilisation du hachage**

- **Vérification de l'intégrité des fichiers:** Le hachage permet de s'assurer qu'un fichier n'a pas été altéré. En comparant le hash du fichier téléchargé avec le hash fourni par l'éditeur, on peut vérifier que le fichier est intact.
- **Stockage des mots de passe:** On ne stocke jamais les mots de passe en clair dans les bases de données. A la place, on stocke leurs hachés. Ainsi, même en cas de piratage de la base de données, les pirates n'auront pas accès aux mots de passe originaux.

**Faiblesses du hachage**

- **Collisions:** Deux données différentes peuvent produire le même hash. Cela peut être exploité pour créer de faux fichiers ou des signatures numériques contrefaites.
- **Attaques par force brute:** Il est possible de tenter de retrouver la donnée originale en essayant tous les hash possibles jusqu'à trouver celui qui correspond. La complexité de l'attaque dépend de la longueur du hash et de la puissance de calcul disponible.

**Le chiffrement**

Le chiffrement est une technique qui permet de transformer une donnée en un message incompréhensible pour quiconque n'a pas la clé de déchiffrement. Le chiffrement est réversible, c'est-à-dire qu'il est possible de retrouver la donnée originale à partir du message chiffré en utilisant la clé de déchiffrement.

**Cas d'utilisation du chiffrement**

- **Sécurisation des communications:** Le chiffrement est utilisé pour protéger les communications en ligne, telles que les emails, les messages instantanés et les transactions bancaires.
- **Protection des données sensibles:** Le chiffrement est utilisé pour protéger les données sensibles stockées sur des ordinateurs, des disques durs ou des clés USB.

**Faiblesses du chiffrement**

- **Faiblesse des clés:** Si la clé de chiffrement est faible ou volée, le message chiffré peut être déchiffré.
- **Attaques par force brute:** Comme pour le hachage, il est possible de tenter de retrouver la clé de chiffrement en essayant toutes les clés possibles jusqu'à trouver la bonne.

# Comment sécuriser les données

**Le hachage**

Le hachage est une technique qui permet de transformer une donnée (texte, fichier, etc.) en une chaîne de caractères unique et de taille fixe appelée "hash". Le hachage est irréversible, c'est-à-dire qu'il est impossible de retrouver la donnée originale à partir du hash.

**Cas d'utilisation du hachage**

- **Vérification de l'intégrité des fichiers:** Le hachage permet de s'assurer qu'un fichier n'a pas été altéré. En comparant le hash du fichier téléchargé avec le hash fourni par l'éditeur, on peut vérifier que le fichier est intact.
- **Stockage des mots de passe:** On ne stocke jamais les mots de passe en clair dans les bases de données. À la place, on stocke leur hash. Ainsi, même en cas de piratage de la base de données, les pirates n'auront pas accès aux mots de passe originaux.

**Faiblesses du hachage**

- **Collisions:** Deux données différentes peuvent produire le même hash. Cela peut être exploité pour créer de faux fichiers ou des signatures numériques contrefaites.
- **Attaques par force brute:** Il est possible de tenter de retrouver la donnée originale en essayant tous les hash possibles jusqu'à trouver celui qui correspond. La complexité de l'attaque dépend de la longueur du hash et de la puissance de calcul disponible.

**Le chiffrement**

Le chiffrement est une technique qui permet de transformer une donnée en un message incompréhensible pour quiconque n'a pas la clé de déchiffrement. Le chiffrement est réversible, c'est-à-dire qu'il est possible de retrouver la donnée originale à partir du message chiffré en utilisant la clé de déchiffrement.

**Cas d'utilisation du chiffrement**

- **Sécurisation des communications:** Le chiffrement est utilisé pour protéger les communications en ligne, telles que les emails, les messages instantanés et les transactions bancaires.
- **Protection des données sensibles:** Le chiffrement est utilisé pour protéger les données sensibles stockées sur des ordinateurs, des disques durs ou des clés USB.

**Faiblesses du chiffrement**

- **Faiblesse des clés:** Si la clé de chiffrement est faible ou volée, le message chiffré peut être déchiffré.
- **Attaques par force brute:** Comme pour le hachage, il est possible de tenter de retrouver la clé de chiffrement en essayant toutes les clés possibles jusqu'à trouver la bonne.

**La signature**

La signature numérique est une technique qui permet de vérifier l'authenticité et l'intégrité d'un message. Elle combine le hachage et le chiffrement pour créer une signature unique qui est liée au message et à l'identité de l'expéditeur.

**Cas d'utilisation de la signature**

- **Signature de documents électroniques:** La signature numérique permet de signer des documents électroniques tels que des contrats, des factures ou des emails. Cela garantit que le document n'a pas été modifié et qu'il provient bien de l'expéditeur indiqué.
- **Authentification des logiciels:** La signature numérique permet de vérifier l'authenticité des logiciels et de s'assurer qu'ils n'ont pas été modifiés par des pirates.

**Faiblesses de la signature**

- **Faiblesse des clés:** Si la clé privée de l'expéditeur est compromise, la signature peut être falsifiée.
- **Attaques par homme au milieu:** Un pirate peut intercepter un message, modifier son contenu et le resigner avec sa propre clé privée. Le destinataire ne pourra pas détecter la fraude.
