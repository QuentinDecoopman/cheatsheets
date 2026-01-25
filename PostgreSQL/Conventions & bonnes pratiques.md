# Conventions & Bonnes Pratiques PostgreSQL

## 📋 Conventions de Nommage

### Tables

- **snake_case** en minuscules
- Pluriel pour les tables

```sql
-- ✅ Bon
CREATE TABLE users (...);
CREATE TABLE order_items (...);
CREATE TABLE user_addresses (...);

-- ❌ Éviter
CREATE TABLE User (...);
CREATE TABLE orderItem (...);
```

### Colonnes

- **snake_case** en minuscules
- Noms descriptifs

```sql
-- ✅ Bon
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email_address VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Contraintes et Index

```sql
-- Primary Key: pk_table_name
CONSTRAINT pk_users PRIMARY KEY (user_id)

-- Foreign Key: fk_table_column_reftable
CONSTRAINT fk_orders_user_id_users FOREIGN KEY (user_id) REFERENCES users(user_id)

-- Index: idx_table_column
CREATE INDEX idx_users_email ON users(email_address);

-- Unique: uq_table_column
CONSTRAINT uq_users_email UNIQUE (email_address)

-- Check: chk_table_column
CONSTRAINT chk_users_age CHECK (age >= 0)
```

---

## 🏗️ Types de Données

### Types recommandés

```sql
-- Nombres entiers
SMALLINT        -- -32768 à 32767
INTEGER (INT)   -- -2147483648 à 2147483647
BIGINT          -- Très grands nombres
SERIAL          -- Auto-incrémentation (INT)
BIGSERIAL       -- Auto-incrémentation (BIGINT)

-- Nombres décimaux
NUMERIC(p, s)   -- Précision exacte (argent)
DECIMAL(p, s)   -- Synonyme de NUMERIC
REAL            -- Virgule flottante simple
DOUBLE PRECISION -- Virgule flottante double

-- Chaînes
CHAR(n)         -- Longueur fixe
VARCHAR(n)      -- Longueur variable
TEXT            -- Longueur illimitée

-- Dates et heures
DATE            -- Date uniquement
TIME            -- Heure uniquement
TIMESTAMP       -- Date et heure
TIMESTAMPTZ     -- Timestamp avec timezone (recommandé)
INTERVAL        -- Durée

-- Booléen
BOOLEAN         -- true/false

-- JSON
JSON            -- Texte JSON
JSONB           -- JSON binaire (recommandé, indexable)

-- Arrays
INTEGER[]       -- Tableau d'entiers
TEXT[]          -- Tableau de textes

-- UUID
UUID            -- Identifiant unique universel

-- Exemples
CREATE TABLE products (
    product_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    price NUMERIC(10, 2) NOT NULL,
    stock INTEGER DEFAULT 0,
    tags TEXT[],
    metadata JSONB,
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

---

## ✅ Bonnes Pratiques

### 1. Utiliser SERIAL ou UUID pour les clés primaires

```sql
-- ✅ SERIAL (auto-increment)
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    name VARCHAR(100)
);

-- ✅ UUID (distribué, unique globalement)
CREATE TABLE users (
    user_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100)
);
```

### 2. Contraintes NOT NULL et DEFAULT

```sql
CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price NUMERIC(10, 2) NOT NULL,
    stock INTEGER NOT NULL DEFAULT 0,
    is_active BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

### 3. Index appropriés

```sql
-- Index simple
CREATE INDEX idx_users_email ON users(email);

-- Index composé
CREATE INDEX idx_orders_user_date ON orders(user_id, order_date DESC);

-- Index partiel
CREATE INDEX idx_active_users_email ON users(email)
WHERE is_active = TRUE;

-- Index unique
CREATE UNIQUE INDEX uq_users_username ON users(LOWER(username));

-- Index GIN pour JSONB et tableaux
CREATE INDEX idx_products_tags ON products USING GIN(tags);
CREATE INDEX idx_products_metadata ON products USING GIN(metadata);

-- Index texte (recherche plein texte)
CREATE INDEX idx_articles_search ON articles
USING GIN(to_tsvector('french', title || ' ' || content));

-- Analyser l'utilisation des index
SELECT * FROM pg_stat_user_indexes WHERE schemaname = 'public';
```

### 4. Foreign Keys avec actions

```sql
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL,
    total_amount NUMERIC(10, 2),
    created_at TIMESTAMPTZ DEFAULT NOW(),

    CONSTRAINT fk_orders_users
        FOREIGN KEY (user_id)
        REFERENCES users(user_id)
        ON DELETE CASCADE      -- Supprimer les commandes si user supprimé
        ON UPDATE CASCADE      -- Mettre à jour si user_id change
);

-- Options :
-- ON DELETE CASCADE : Supprime les lignes dépendantes
-- ON DELETE SET NULL : Met à NULL
-- ON DELETE RESTRICT : Empêche la suppression (par défaut)
-- ON DELETE NO ACTION : Similaire à RESTRICT
```

---

## 🎯 Requêtes Avancées

### CTEs (Common Table Expressions)

```sql
-- CTE simple
WITH active_users AS (
    SELECT user_id, name, email
    FROM users
    WHERE is_active = TRUE
)
SELECT * FROM active_users
WHERE email LIKE '%@gmail.com';

-- CTE multiple
WITH
user_stats AS (
    SELECT
        user_id,
        COUNT(*) as order_count,
        SUM(total_amount) as total_spent
    FROM orders
    GROUP BY user_id
),
high_value_users AS (
    SELECT user_id
    FROM user_stats
    WHERE total_spent > 1000
)
SELECT u.name, us.order_count, us.total_spent
FROM users u
JOIN user_stats us ON u.user_id = us.user_id
WHERE u.user_id IN (SELECT user_id FROM high_value_users);

-- CTE récursive
WITH RECURSIVE category_tree AS (
    -- Cas de base
    SELECT id, name, parent_id, 1 as level
    FROM categories
    WHERE parent_id IS NULL

    UNION ALL

    -- Cas récursif
    SELECT c.id, c.name, c.parent_id, ct.level + 1
    FROM categories c
    INNER JOIN category_tree ct ON c.parent_id = ct.id
)
SELECT * FROM category_tree ORDER BY level, name;
```

### Window Functions

```sql
-- ROW_NUMBER, RANK, DENSE_RANK
SELECT
    name,
    department,
    salary,
    ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) as row_num,
    RANK() OVER (PARTITION BY department ORDER BY salary DESC) as rank,
    DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) as dense_rank
FROM employees;

-- Running total
SELECT
    order_date,
    amount,
    SUM(amount) OVER (ORDER BY order_date) as running_total
FROM orders;

-- Moving average
SELECT
    date,
    value,
    AVG(value) OVER (
        ORDER BY date
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) as moving_avg_7days
FROM metrics;

-- LAG et LEAD
SELECT
    date,
    revenue,
    LAG(revenue) OVER (ORDER BY date) as previous_day,
    LEAD(revenue) OVER (ORDER BY date) as next_day,
    revenue - LAG(revenue) OVER (ORDER BY date) as change
FROM daily_sales;

-- FIRST_VALUE et LAST_VALUE
SELECT
    name,
    department,
    salary,
    FIRST_VALUE(salary) OVER (
        PARTITION BY department
        ORDER BY salary DESC
    ) as highest_in_dept
FROM employees;
```

### JSONB Operations

```sql
-- Créer et insérer
INSERT INTO products (name, metadata)
VALUES ('Product 1', '{"color": "red", "size": "L", "tags": ["new", "sale"]}');

-- Extraire des données
SELECT
    name,
    metadata->>'color' as color,              -- Texte
    metadata->'tags' as tags,                 -- JSON
    (metadata->>'price')::NUMERIC as price    -- Convertir en nombre
FROM products;

-- Filtrer par JSONB
SELECT * FROM products
WHERE metadata->>'color' = 'red';

SELECT * FROM products
WHERE metadata @> '{"color": "red"}';        -- Contient

SELECT * FROM products
WHERE metadata ? 'color';                     -- Clé existe

SELECT * FROM products
WHERE metadata->'tags' @> '"sale"';          -- Array contient

-- Mettre à jour JSONB
UPDATE products
SET metadata = metadata || '{"featured": true}'
WHERE product_id = 1;

UPDATE products
SET metadata = jsonb_set(metadata, '{price}', '29.99')
WHERE product_id = 1;

-- Supprimer une clé
UPDATE products
SET metadata = metadata - 'old_field'
WHERE product_id = 1;
```

### Full-Text Search

```sql
-- Créer un index de recherche
CREATE INDEX idx_articles_search ON articles
USING GIN(to_tsvector('french', title || ' ' || content));

-- Recherche
SELECT
    title,
    ts_rank(to_tsvector('french', title || ' ' || content), query) as rank
FROM articles, to_tsquery('french', 'postgresql & performance') query
WHERE to_tsvector('french', title || ' ' || content) @@ query
ORDER BY rank DESC;

-- Avec colonne générée (PostgreSQL 12+)
ALTER TABLE articles
ADD COLUMN search_vector tsvector
GENERATED ALWAYS AS (
    to_tsvector('french', coalesce(title, '') || ' ' || coalesce(content, ''))
) STORED;

CREATE INDEX idx_articles_search ON articles USING GIN(search_vector);

-- Recherche simplifiée
SELECT * FROM articles
WHERE search_vector @@ to_tsquery('french', 'postgresql');
```

---

## 🚀 Performance

### EXPLAIN ANALYZE

```sql
-- Analyser une requête
EXPLAIN ANALYZE
SELECT u.name, COUNT(o.order_id)
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
GROUP BY u.user_id, u.name;

-- Format détaillé
EXPLAIN (ANALYZE, BUFFERS, VERBOSE)
SELECT * FROM products WHERE price > 100;
```

### Vacuum et Analyze

```sql
-- Nettoyer et optimiser
VACUUM ANALYZE users;

-- Vacuum complet (plus agressif)
VACUUM FULL users;

-- Analyze uniquement (mise à jour des statistiques)
ANALYZE users;

-- Auto-vacuum (configuré automatiquement)
-- Voir les paramètres dans postgresql.conf
```

### Partitionnement

```sql
-- Partitionnement par plage
CREATE TABLE orders (
    order_id BIGSERIAL,
    user_id INTEGER,
    total_amount NUMERIC(10, 2),
    order_date DATE NOT NULL
) PARTITION BY RANGE (order_date);

-- Créer les partitions
CREATE TABLE orders_2024_q1 PARTITION OF orders
FOR VALUES FROM ('2024-01-01') TO ('2024-04-01');

CREATE TABLE orders_2024_q2 PARTITION OF orders
FOR VALUES FROM ('2024-04-01') TO ('2024-07-01');

-- Partitionnement par liste
CREATE TABLE users_by_country (
    user_id SERIAL,
    country VARCHAR(2)
) PARTITION BY LIST (country);

CREATE TABLE users_france PARTITION OF users_by_country
FOR VALUES IN ('FR');

CREATE TABLE users_belgium PARTITION OF users_by_country
FOR VALUES IN ('BE');
```

---

## 🔄 Transactions

### Transactions de base

```sql
BEGIN;

UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;

-- Vérifier
SELECT balance FROM accounts WHERE account_id IN (1, 2);

COMMIT;
-- Ou ROLLBACK; pour annuler
```

### Niveaux d'isolation

```sql
-- READ UNCOMMITTED (non supporté, équivalent à READ COMMITTED)
BEGIN TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;

-- READ COMMITTED (par défaut)
BEGIN TRANSACTION ISOLATION LEVEL READ COMMITTED;

-- REPEATABLE READ
BEGIN TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- SERIALIZABLE
BEGIN TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

### Savepoints

```sql
BEGIN;

INSERT INTO users (name, email) VALUES ('John', 'john@example.com');

SAVEPOINT my_savepoint;

UPDATE users SET name = 'Jane' WHERE email = 'john@example.com';

-- Revenir au savepoint
ROLLBACK TO SAVEPOINT my_savepoint;

COMMIT;
```

---

## 🎨 Fonctions et Procédures

### Fonctions

```sql
-- Fonction simple
CREATE OR REPLACE FUNCTION get_user_order_count(p_user_id INTEGER)
RETURNS INTEGER
LANGUAGE SQL
AS $$
    SELECT COUNT(*)::INTEGER
    FROM orders
    WHERE user_id = p_user_id;
$$;

-- Utilisation
SELECT get_user_order_count(123);

-- Fonction avec PL/pgSQL
CREATE OR REPLACE FUNCTION create_user(
    p_name VARCHAR,
    p_email VARCHAR
)
RETURNS INTEGER
LANGUAGE plpgsql
AS $$
DECLARE
    v_user_id INTEGER;
BEGIN
    INSERT INTO users (name, email)
    VALUES (p_name, p_email)
    RETURNING user_id INTO v_user_id;

    RETURN v_user_id;
END;
$$;

-- Fonction avec logique complexe
CREATE OR REPLACE FUNCTION calculate_discount(
    p_user_id INTEGER,
    p_amount NUMERIC
)
RETURNS NUMERIC
LANGUAGE plpgsql
AS $$
DECLARE
    v_order_count INTEGER;
    v_discount NUMERIC;
BEGIN
    SELECT COUNT(*) INTO v_order_count
    FROM orders
    WHERE user_id = p_user_id;

    IF v_order_count > 10 THEN
        v_discount := p_amount * 0.15;
    ELSIF v_order_count > 5 THEN
        v_discount := p_amount * 0.10;
    ELSE
        v_discount := p_amount * 0.05;
    END IF;

    RETURN v_discount;
END;
$$;
```

### Procédures stockées (PostgreSQL 11+)

```sql
CREATE OR REPLACE PROCEDURE update_user_stats()
LANGUAGE plpgsql
AS $$
BEGIN
    UPDATE user_stats us
    SET
        order_count = (
            SELECT COUNT(*) FROM orders WHERE user_id = us.user_id
        ),
        total_spent = (
            SELECT COALESCE(SUM(total_amount), 0)
            FROM orders WHERE user_id = us.user_id
        ),
        updated_at = NOW();

    COMMIT;
END;
$$;

-- Appel
CALL update_user_stats();
```

### Triggers

```sql
-- Fonction trigger
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$;

-- Créer le trigger
CREATE TRIGGER users_updated_at_trigger
BEFORE UPDATE ON users
FOR EACH ROW
EXECUTE FUNCTION update_updated_at_column();

-- Trigger pour audit
CREATE TABLE audit_log (
    log_id SERIAL PRIMARY KEY,
    table_name VARCHAR(50),
    operation VARCHAR(10),
    old_data JSONB,
    new_data JSONB,
    changed_by VARCHAR(50),
    changed_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE OR REPLACE FUNCTION audit_trigger_function()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        INSERT INTO audit_log (table_name, operation, new_data, changed_by)
        VALUES (TG_TABLE_NAME, TG_OP, to_jsonb(NEW), current_user);
        RETURN NEW;
    ELSIF TG_OP = 'UPDATE' THEN
        INSERT INTO audit_log (table_name, operation, old_data, new_data, changed_by)
        VALUES (TG_TABLE_NAME, TG_OP, to_jsonb(OLD), to_jsonb(NEW), current_user);
        RETURN NEW;
    ELSIF TG_OP = 'DELETE' THEN
        INSERT INTO audit_log (table_name, operation, old_data, changed_by)
        VALUES (TG_TABLE_NAME, TG_OP, to_jsonb(OLD), current_user);
        RETURN OLD;
    END IF;
END;
$$;

CREATE TRIGGER users_audit_trigger
AFTER INSERT OR UPDATE OR DELETE ON users
FOR EACH ROW
EXECUTE FUNCTION audit_trigger_function();
```

---

## 🔒 Sécurité

### Roles et Permissions

```sql
-- Créer un rôle
CREATE ROLE readonly_user WITH LOGIN PASSWORD 'secure_password';

-- Accorder des permissions
GRANT CONNECT ON DATABASE mydb TO readonly_user;
GRANT USAGE ON SCHEMA public TO readonly_user;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly_user;

-- Permissions futures
ALTER DEFAULT PRIVILEGES IN SCHEMA public
GRANT SELECT ON TABLES TO readonly_user;

-- Révoquer
REVOKE SELECT ON users FROM readonly_user;

-- Rôle avec permissions limitées
CREATE ROLE app_user WITH LOGIN PASSWORD 'password';
GRANT CONNECT ON DATABASE mydb TO app_user;
GRANT USAGE ON SCHEMA public TO app_user;
GRANT SELECT, INSERT, UPDATE ON users TO app_user;
GRANT USAGE, SELECT ON SEQUENCE users_user_id_seq TO app_user;
```

### Row Level Security (RLS)

```sql
-- Activer RLS
ALTER TABLE documents ENABLE ROW LEVEL SECURITY;

-- Créer une policy
CREATE POLICY user_documents ON documents
FOR ALL
TO app_user
USING (owner_id = current_setting('app.current_user_id')::INTEGER);

-- Policy pour admin
CREATE POLICY admin_all ON documents
FOR ALL
TO admin_role
USING (true);

-- Définir la variable de session
SET app.current_user_id = 123;
```

---

## 📊 Vues et Vues Matérialisées

### Vues

```sql
-- Vue simple
CREATE VIEW active_user_summary AS
SELECT
    u.user_id,
    u.name,
    u.email,
    COUNT(o.order_id) as total_orders,
    COALESCE(SUM(o.total_amount), 0) as lifetime_value
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
WHERE u.is_active = TRUE
GROUP BY u.user_id, u.name, u.email;

-- Utilisation
SELECT * FROM active_user_summary WHERE total_orders > 5;
```

### Vues Matérialisées

```sql
-- Créer une vue matérialisée
CREATE MATERIALIZED VIEW monthly_sales AS
SELECT
    DATE_TRUNC('month', order_date) as month,
    COUNT(*) as order_count,
    SUM(total_amount) as total_sales
FROM orders
GROUP BY DATE_TRUNC('month', order_date)
ORDER BY month DESC;

-- Créer un index sur la vue matérialisée
CREATE INDEX idx_monthly_sales_month ON monthly_sales(month);

-- Rafraîchir la vue
REFRESH MATERIALIZED VIEW monthly_sales;

-- Rafraîchir sans bloquer les lectures
REFRESH MATERIALIZED VIEW CONCURRENTLY monthly_sales;
```

---

## 📚 Ressources

- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [PostgreSQL Tutorial](https://www.postgresqltutorial.com/)
- [Use The Index, Luke](https://use-the-index-luke.com/)
- [PgExercises](https://pgexercises.com/)
