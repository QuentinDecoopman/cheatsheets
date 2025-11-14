# PostgreSQL

## Documentation

https://www.postgresql.org/docs/

## Installation (exemples)

- Debian/Ubuntu :

```sh
sudo apt update
sudo apt install postgresql postgresql-contrib -y
```

- CentOS / RHEL :

```sh
sudo yum install postgresql-server postgresql-contrib -y
sudo postgresql-setup initdb
sudo systemctl enable --now postgresql
```

## Démarrer/arrêter le service

```sh
sudo systemctl start postgresql
sudo systemctl stop postgresql
sudo systemctl status postgresql
```

## Compte et accès

- Par défaut, PostgreSQL crée un utilisateur système `postgres`.

Se connecter en tant que `postgres` :

```sh
sudo -i -u postgres
psql
```

Créer un utilisateur et une base :

```sql
-- dans psql
CREATE ROLE mon_user WITH LOGIN PASSWORD 'motdepasse';
CREATE DATABASE ma_base OWNER mon_user;
GRANT ALL PRIVILEGES ON DATABASE ma_base TO mon_user;
```

Connexion depuis l'extérieur : définir la `DATABASE_URL` ou utiliser psql :

```sh
psql postgresql://mon_user:motdepasse@localhost:5432/ma_base
```

## Commandes psql utiles

- Lister les bases : `\l`
- Se connecter à une base : `\c ma_base`
- Lister les tables : `\dt`
- Voir les rôles : `\du`
- Exécuter un fichier SQL : `\i fichier.sql`

## Sauvegarde et restauration

- Sauvegarde (dump complet) :

```sh
pg_dump -U mon_user -h localhost -F c -b -v -f ma_base.dump ma_base
```

- Restauration :

```sh
pg_restore -U mon_user -h localhost -d ma_base -v ma_base.dump
```

- Sauvegarde SQL (textuelle) :

```sh
pg_dump -U mon_user -h localhost -F p -v -f ma_base.sql ma_base
psql -U mon_user -d ma_base -f ma_base.sql
```

## Performance & maintenance

- Vérifier l'espace et bloat : `VACUUM` / `VACUUM ANALYZE` / `VACUUM FULL` (attention `FULL` bloque).
- Exécuter `ANALYZE` pour mettre à jour les statistiques.
- Planifier `autovacuum` (activé par défaut).
- Indexation : `CREATE INDEX idx_col ON table (col);` ; pour expressions ou recherches textuelles, utiliser `GIN`.

## Extensions utiles

- `pg_stat_statements` : suivi des requêtes lentes.
- `postgis` : données géospatiales.

Activer une extension (dans la base) :

```sql
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
```

## Connexion depuis une application

Chaîne de connexion courante :

```
postgresql://USER:PASSWORD@HOST:PORT/DATABASE
```

Exemple Node.js (pg) :

```js
import { Pool } from "pg";

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
});

const res = await pool.query("SELECT NOW()");
console.log(res.rows[0]);
```

## Sécurité

- Configurer `pg_hba.conf` pour restreindre les accès (auth methods: md5, scram-sha-256, peer).
- Utiliser des roles limités pour les applications.
- Sauvegarder régulièrement et tester les restaurations.

## Commandes courantes

Voici une liste rapide des commandes PostgreSQL les plus utilisées (psql / shell) :

- Se connecter à la base en tant que user local :

```sh
psql -U mon_user -d ma_base -h localhost
```

- Se connecter via DATABASE_URL :

```sh
psql "$DATABASE_URL"
```

- Commandes psql utiles (préfixer par `psql` puis taper ces métacommandes) :

```sql
\l        -- lister les bases
\c ma_base -- se connecter à une base
\dt       -- lister les tables
\d+ nom_table -- décrire une table (colonnes, index, taille)
\du       -- lister les rôles
\x        -- basculer en affichage étendu
\q        -- quitter psql
```

- Gestion des migrations / schéma (exemples) :

```sh
psql -f migrations/001_init.sql -d ma_base
```

- Sauvegarde/restauration rapide :

```sh
pg_dump -U mon_user -h localhost -F p ma_base > ma_base.sql
psql -U mon_user -d ma_base -f ma_base.sql
```

- Arrêter/relancer le service :

```sh
sudo systemctl restart postgresql
sudo systemctl reload postgresql
```

- Maintenance et diagnostic :

```sql
VACUUM;
VACUUM ANALYZE;
ANALYZE;
EXPLAIN ANALYZE SELECT * FROM table WHERE ...;
SELECT pid, state, query FROM pg_stat_activity WHERE datname = 'ma_base';
SELECT * FROM pg_stat_statements ORDER BY total_time DESC LIMIT 10;
```

- Gérer les extensions :

```sql
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
```

## Ressources

- Documentation officielle : https://www.postgresql.org/docs/
- Tutoriel : https://www.postgresqltutorial.com/


