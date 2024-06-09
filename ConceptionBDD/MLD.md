# MLD : Modèle logique de données

Déduction du MLD à partir du MCD :
- Règle 1 :
    * Entité du MCD devient une table (relation)
    * Attributs deviennent des champs ou colonnes uniques
    * Identifiant (id auto-généré par le SGBD) correspond à la clé primaire


- Règle 2 : Faire disparaitre les associations binaires avec l’ajout de clés étrangères dans la table fils qui fait référence à la clé primaire de la table père.


- Règle 3 : L’association « many to many » devient une table d’association (Association_date_lieu)