# MLD : Modèle logique de données

- [Index](/Readme.md)

Le MLD est déduit du MCD et prépare l'implémentation dans un SGBD relationnel. Il transforme les concepts du MCD
en structures logiques (tables, colonnes, clés, contraintes).

Règles courantes pour la transformation :

- Entité du MCD -> table (relation).
- Attributs -> colonnes de la table.
- Identifiant -> clé primaire (par exemple `id` auto‑généré si besoin).
- Associations binaires simples -> ajout d'une clé étrangère dans la table enfant qui référence la clé primaire de la
  table parent.
- Association many-to-many -> création d'une table d'association (ex. `Association_date_lieu`) qui contient les
  clés étrangères vers les deux tables liées et éventuellement des attributs propres à la relation.

Ces règles sont des lignes directrices : selon le contexte et le SGBD, il peut y avoir des variations ou des optimisations
à appliquer.
# MLD : Modèle logique de données

- [Index](/Readme.md)

Déduction du MLD à partir du MCD :
- Règle 1 :
    * Entité du MCD devient une table (relation)
    * Attributs deviennent des champs ou colonnes uniques
    * Identifiant (id auto-généré par le SGBDR) correspond à la clé primaire


- Règle 2 : Faire disparaitre les associations binaires avec l’ajout de clés étrangères dans la table fils qui fait référence à la clé primaire de la table père.


- Règle 3 : L’association « many to many » devient une table d’association (Association_date_lieu)
