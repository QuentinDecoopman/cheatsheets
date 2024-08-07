# Cheatsheet SQL

- [Index](/Readme.md)
- [Doc](https://sql.sh/)

## Création de tables

```sql
CREATE TABLE nom_table (
    colonne1 type1,
    colonne2 type2,
    ...
);
```

## Insertion de données

```sql
INSERT INTO nom_table (colonne1, colonne2, ...)
VALUES (valeur1, valeur2, ...);
```

## Sélection de données

```sql
SELECT colonne1, colonne2, ...
FROM nom_table
WHERE condition;
```

## Mise à jour de données

```sql
UPDATE nom_table
SET colonne1 = nouvelle_valeur1, colonne2 = nouvelle_valeur2, ...
WHERE condition;
```

## Suppression de données

```sql
DELETE FROM nom_table
WHERE condition;
```

## Clause ORDER BY

```sql
SELECT colonne1, colonne2, ...
FROM nom_table
ORDER BY colonne1 ASC, colonne2 DESC;
```

## Clause GROUP BY

```sql
SELECT colonne, COUNT(*)
FROM nom_table
GROUP BY colonne;
```

## Jointures

### Inner join

```sql
SELECT colonne1, colonne2, ...
FROM table1
INNER JOIN table2 ON table1.colonne = table2.colonne;
```

### Left Join

```sql
SELECT colonne1, colonne2, ...
FROM table1
LEFT JOIN table2 ON table1.colonne = table2.colonne;
```

## Agrégations

```sql
SELECT AVG(colonne) AS moyenne, SUM(colonne) AS somme
FROM nom_table
WHERE condition;
```

## Filtrage avec LIKE

```sql
SELECT colonne1, colonne2, ...
FROM nom_table
WHERE colonne LIKE 'valeur%';
```

## Utilisation de LIMIT

```sql
SELECT colonne1, colonne2, ...
FROM nom_table
LIMIT 10;
```

## Création d'index

```sql
CREATE INDEX index_nom ON nom_table (colonne);
```

## Suppression de tables

```sql
DROP TABLE nom_table;
```

## Sauvegarde et Restauration

```bash
### Exporter une base de données
mysqldump -u utilisateur -p nom_base_de_donnees > backup.sql

### Importer une base de données
mysql -u utilisateur -p nom_base_de_donnees < backup.sql
```
