# Méthode Merise

- [Index](/Readme.md)

Merise est une méthode de modélisation utilisée pour analyser la structure à informatiser et cadrer les projets
informatiques. Elle facilite la communication entre utilisateurs et informaticiens en séparant les préoccupations
et en proposant des représentations normalisées (MCD, MLD, MPD, etc.).

Créée dans les années 1970, Merise a été largement utilisée en France et en Europe pour structurer des projets de
grande envergure. Elle s'appuie sur le modèle relationnel et aide à concevoir des bases de données cohérentes.

Concrètement, Merise permet de :

- Hiérarchiser les préoccupations du projet.
- Décrire le fonctionnement du système à informatiser :
  - Les données (MCD) : relations et dépendances entre acteurs (ex. client – commande – produit – fournisseur).
  - Les traitements (MCT) : flux et processus métier (ex. comment une commande est traitée).
- Proposer une implémentation logique (MLD) puis physique (MPD) des données.

Les niveaux principaux sont :

- **MCD** : conceptuel, se concentre sur les entités et leurs relations.
- **MLD** : logique, définit les tables, attributs et contraintes d'intégrité.
- **MPD** : physique, précise les types de données, index, clés et optimisations liées au SGBD.

Ces niveaux permettent de passer d'une vue métier à une implémentation technique de manière structurée.
# Méthode Merise

- [Index](/Readme.md)

Merise est une méthode informatique dédiée à la modélisation qui analyse la structure à informatiser en terme de
systèmes. Le gros avantage de cette méthode est qu’elle permet de cadrer le projet informatique et de « discuter » en se
comprenant entre utilisateurs et informaticiens.

Créée dans les années 70 sur commande de l’État français et destinée aux gros projets informatiques de l’époque, la
méthode a perduré jusqu’à aujourd’hui. Son utilisation très répandue en Europe constitue un socle difficilement
contournable lorsque l’on s’attache à la création de bases de données.

Merise est en fait un outil analytique qui facilite la création de base de données et de projets informatique. Le
principal auteur de la méthode est Hubert Tardieu qui se basa sur les travaux autour du modèle relationnel de Codd.

Concrètement Merise (que l’on prononce Meurise) permet de :

- Hiérarchiser les préoccupations du gestionnaire de projet informatique


- Décrire le fonctionnement du système à informatiser et notamment :

    - Les données (MCD) : quelles sont les relations et les dépendances entre les différents acteurs (client –
      commande – produit – fournisseur par exemple)


- Les traitements (MCT) : comment les acteurs travaillent-ils ensemble (comment se passe une commande concrètement par
  exemple)


- Proposer une implémentation logique (MLD, MLT) du point précédent


- Proposer une construction concrète et utilisable du point précédent (MPD, MOT)

Le MCD (Modèle Conceptuel de Données) représente les entités principales d'un système, ainsi que leurs relations et
attributs. Il utilise généralement des symboles tels que des rectangles pour les entités, des lignes pour les relations
et des losanges pour les cardinalités.

Le MLD (Modèle Logique de Données) affine le MCD en définissant les tables de la base de données, leurs attributs et les
contraintes d'intégrité. Il élimine les ambiguïtés et les redondances.

Le MPD (Modèle Physique de Données) est la représentation concrète de la structure de la base de données. Il détaille
les types de données, les index, les clés primaires et étrangères, ainsi que toute autre spécificité liée à la mise en
œuvre physique des données.

Les principales différences résident dans leur niveau d'abstraction.

**Le MCD est conceptuel et se concentre sur les entités et leurs relations.**

**Le MLD est plus proche de l'implémentation logique en définissant les tables et leurs attributs.**

**Le MPD est le plus proche de l'implémentation physique en spécifiant les détails concrets de la base de données.**

Les cardinalités indiquent les relations entre les entités et sont généralement exprimées sous forme de ratios
numériques.