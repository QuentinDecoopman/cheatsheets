# Conventions & Bonnes Pratiques SQL

## 📋 Conventions de Nommage

### Tables

- **snake_case** en minuscules
- Pluriel pour les tables de données

```sql
-- ✅ Bon
CREATE TABLE users (...);
CREATE TABLE order_items (...);
CREATE TABLE product_categories (...);

-- ❌ Éviter
CREATE TABLE User (...);
CREATE TABLE OrderItem (...);
```

### Colonnes

- **snake_case** en minuscules
- Noms descriptifs

```sql
-- ✅ Bon
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email_address VARCHAR(100),
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);

-- ❌ Éviter
CREATE TABLE users (
    id INT,
    fn VARCHAR(50),
    ln VARCHAR(50)
);
```

### Clés et Index

```sql
-- Primary Key
pk_table_name

-- Foreign Key
fk_table_name_referenced_table

-- Index
idx_table_name_column_name

-- Unique constraint
uq_table_name_column_name

-- Exemple
CREATE TABLE orders (
    order_id INT,
    user_id INT,
    CONSTRAINT pk_orders PRIMARY KEY (order_id),
    CONSTRAINT fk_orders_users FOREIGN KEY (user_id) REFERENCES users(user_id)
);

CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE UNIQUE INDEX uq_users_email ON users(email_address);
```

---

## ✅ Bonnes Pratiques

### 1. Requêtes formatées

```sql
-- ✅ Bon - lisible et formaté
SELECT
    u.user_id,
    u.first_name,
    u.last_name,
    COUNT(o.order_id) AS total_orders
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
WHERE u.is_active = TRUE
    AND u.created_at >= '2024-01-01'
GROUP BY
    u.user_id,
    u.first_name,
    u.last_name
HAVING COUNT(o.order_id) > 0
ORDER BY total_orders DESC
LIMIT 10;

-- ❌ Éviter
select u.user_id,u.first_name,count(o.order_id) from users u left join orders o on u.user_id=o.user_id where u.is_active=true group by u.user_id order by count(o.order_id) desc limit 10;
```

### 2. Mots-clés en MAJUSCULES

```sql
-- ✅ Bon
SELECT name, email
FROM users
WHERE status = 'active'
ORDER BY created_at DESC;

-- ❌ Éviter (mais fonctionnel)
select name, email
from users
where status = 'active'
order by created_at desc;
```

### 3. Alias explicites

```sql
-- ✅ Bon
SELECT
    u.user_id,
    u.name AS user_name,
    COUNT(o.order_id) AS order_count,
    SUM(o.total_amount) AS total_spent
FROM users u
JOIN orders o ON u.user_id = o.user_id;

-- ❌ Moins clair
SELECT
    u.user_id,
    u.name,
    COUNT(o.order_id),
    SUM(o.total_amount)
FROM users u
JOIN orders o ON u.user_id = o.user_id;
```

### 4. Éviter SELECT \*

```sql
-- ❌ Mauvais - non performant et peu clair
SELECT * FROM users;

-- ✅ Bon - spécifier les colonnes
SELECT
    user_id,
    first_name,
    last_name,
    email
FROM users;
```

### 5. Utiliser les JOINs explicites

```sql
-- ✅ Bon - INNER JOIN explicite
SELECT
    u.name,
    o.order_date
FROM users u
INNER JOIN orders o ON u.user_id = o.user_id;

-- ❌ Éviter les jointures implicites
SELECT
    u.name,
    o.order_date
FROM users u, orders o
WHERE u.user_id = o.user_id;
```

---

## 🏗️ Conception de Base de Données

### Types de données appropriés

```sql
-- ✅ Choisir les bons types
CREATE TABLE users (
    user_id BIGINT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL,
    age TINYINT UNSIGNED,
    balance DECIMAL(10, 2),
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

-- Types courants :
-- INT, BIGINT : Nombres entiers
-- DECIMAL(p,s) : Nombres décimaux précis (argent)
-- FLOAT, DOUBLE : Nombres à virgule flottante
-- VARCHAR(n) : Chaînes variables
-- TEXT : Texte long
-- DATE, DATETIME, TIMESTAMP : Dates et heures
-- BOOLEAN : Vrai/Faux
-- ENUM : Valeurs prédéfinies
```

### Contraintes

```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    sku VARCHAR(50) NOT NULL UNIQUE,
    price DECIMAL(10, 2) NOT NULL CHECK (price >= 0),
    stock INT DEFAULT 0 CHECK (stock >= 0),
    category_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_products_categories
        FOREIGN KEY (category_id)
        REFERENCES categories(category_id)
        ON DELETE SET NULL
        ON UPDATE CASCADE,

    CONSTRAINT uq_products_sku UNIQUE (sku)
);
```

### Indexation

```sql
-- Index simple
CREATE INDEX idx_users_email ON users(email);

-- Index composé
CREATE INDEX idx_orders_user_date ON orders(user_id, order_date);

-- Index unique
CREATE UNIQUE INDEX uq_users_username ON users(username);

-- Index full-text (recherche textuelle)
CREATE FULLTEXT INDEX ft_products_description ON products(description);

-- ✅ Indexer les colonnes fréquemment utilisées dans :
-- - WHERE
-- - JOIN
-- - ORDER BY
-- - GROUP BY
```

---

## 🎯 Requêtes Optimisées

### 1. WHERE au lieu de HAVING quand possible

```sql
-- ✅ Bon - filtre avant l'agrégation
SELECT
    category_id,
    COUNT(*) AS product_count
FROM products
WHERE price > 100
GROUP BY category_id;

-- ❌ Moins efficace
SELECT
    category_id,
    COUNT(*) AS product_count
FROM products
GROUP BY category_id
HAVING price > 100; -- Erreur ici de toute façon
```

### 2. EXISTS vs IN

```sql
-- ✅ EXISTS souvent plus rapide pour grandes tables
SELECT *
FROM users u
WHERE EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.user_id = u.user_id
);

-- IN pour petits ensembles
SELECT *
FROM users
WHERE user_id IN (1, 2, 3, 4, 5);
```

### 3. LIMIT pour pagination

```sql
-- ✅ Bon
SELECT *
FROM products
ORDER BY created_at DESC
LIMIT 20 OFFSET 0;

-- Page 2
SELECT *
FROM products
ORDER BY created_at DESC
LIMIT 20 OFFSET 20;
```

### 4. UNION vs UNION ALL

```sql
-- UNION ALL plus rapide (pas de dédoublonnage)
SELECT name FROM customers
UNION ALL
SELECT name FROM suppliers;

-- UNION si dédoublonnage nécessaire
SELECT name FROM customers
UNION
SELECT name FROM suppliers;
```

---

## 🔄 Transactions

### Transactions ACID

```sql
-- ✅ Utiliser des transactions
START TRANSACTION;

UPDATE accounts
SET balance = balance - 100
WHERE account_id = 1;

UPDATE accounts
SET balance = balance + 100
WHERE account_id = 2;

-- Vérifier et valider
COMMIT;

-- Ou annuler en cas d'erreur
-- ROLLBACK;
```

### Niveaux d'isolation

```sql
-- READ UNCOMMITTED
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;

-- READ COMMITTED (par défaut souvent)
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;

-- REPEATABLE READ
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- SERIALIZABLE
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

---

## 🛡️ Sécurité

### 1. Requêtes préparées (éviter les injections SQL)

```sql
-- ✅ Bon (en utilisant des paramètres préparés côté application)
-- Exemple en pseudo-code
PREPARE stmt FROM 'SELECT * FROM users WHERE email = ?';
EXECUTE stmt USING @email;

-- ❌ JAMAIS de concaténation directe
-- "SELECT * FROM users WHERE email = '" + userInput + "'"
```

### 2. Privilèges minimaux

```sql
-- ✅ Créer des utilisateurs avec droits limités
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'strong_password';

-- Accorder uniquement les privilèges nécessaires
GRANT SELECT, INSERT, UPDATE ON mydb.* TO 'app_user'@'localhost';

-- Lecture seule pour analytics
CREATE USER 'analytics'@'localhost' IDENTIFIED BY 'password';
GRANT SELECT ON mydb.* TO 'analytics'@'localhost';

-- Révoquer les privilèges
REVOKE INSERT ON mydb.* FROM 'app_user'@'localhost';
```

### 3. Validation des données

```sql
-- ✅ Contraintes de validation
CREATE TABLE users (
    email VARCHAR(100) CHECK (email LIKE '%@%.%'),
    age INT CHECK (age >= 18 AND age <= 120),
    phone VARCHAR(20) CHECK (phone REGEXP '^[0-9+()-]+$')
);
```

---

## 🎨 Fonctions et Agrégations

### Fonctions d'agrégation

```sql
SELECT
    COUNT(*) AS total_users,
    COUNT(DISTINCT country) AS unique_countries,
    AVG(age) AS average_age,
    MIN(created_at) AS first_user_date,
    MAX(created_at) AS last_user_date,
    SUM(order_total) AS total_revenue
FROM users;
```

### Fonctions de chaînes

```sql
SELECT
    CONCAT(first_name, ' ', last_name) AS full_name,
    UPPER(email) AS email_upper,
    LOWER(email) AS email_lower,
    LENGTH(username) AS username_length,
    SUBSTRING(description, 1, 100) AS short_description,
    TRIM(name) AS trimmed_name,
    REPLACE(phone, '-', '') AS phone_clean
FROM users;
```

### Fonctions de dates

```sql
SELECT
    NOW() AS current_datetime,
    CURDATE() AS current_date,
    DATE(created_at) AS creation_date,
    YEAR(created_at) AS creation_year,
    MONTH(created_at) AS creation_month,
    DAY(created_at) AS creation_day,
    DATEDIFF(NOW(), created_at) AS days_since_creation,
    DATE_ADD(created_at, INTERVAL 30 DAY) AS expiration_date,
    DATE_FORMAT(created_at, '%Y-%m-%d %H:%i') AS formatted_date
FROM orders;
```

### CASE Statements

```sql
SELECT
    product_name,
    price,
    CASE
        WHEN price < 10 THEN 'Budget'
        WHEN price BETWEEN 10 AND 50 THEN 'Standard'
        WHEN price > 50 THEN 'Premium'
        ELSE 'Unknown'
    END AS price_category
FROM products;

-- Avec agrégation
SELECT
    category,
    SUM(CASE WHEN status = 'sold' THEN 1 ELSE 0 END) AS sold_count,
    SUM(CASE WHEN status = 'available' THEN 1 ELSE 0 END) AS available_count
FROM products
GROUP BY category;
```

---

## 🔍 Requêtes Avancées

### Sous-requêtes

```sql
-- Dans SELECT
SELECT
    u.name,
    (SELECT COUNT(*) FROM orders WHERE user_id = u.user_id) AS order_count
FROM users u;

-- Dans WHERE
SELECT *
FROM products
WHERE price > (SELECT AVG(price) FROM products);

-- Dans FROM
SELECT
    avg_prices.category,
    avg_prices.avg_price
FROM (
    SELECT
        category,
        AVG(price) AS avg_price
    FROM products
    GROUP BY category
) AS avg_prices
WHERE avg_prices.avg_price > 100;
```

### Window Functions

```sql
-- ROW_NUMBER
SELECT
    name,
    category,
    price,
    ROW_NUMBER() OVER (PARTITION BY category ORDER BY price DESC) AS rank
FROM products;

-- RANK et DENSE_RANK
SELECT
    name,
    score,
    RANK() OVER (ORDER BY score DESC) AS rank,
    DENSE_RANK() OVER (ORDER BY score DESC) AS dense_rank
FROM players;

-- Running total
SELECT
    order_date,
    amount,
    SUM(amount) OVER (ORDER BY order_date) AS running_total
FROM orders;

-- Moving average
SELECT
    date,
    value,
    AVG(value) OVER (
        ORDER BY date
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS moving_avg_7days
FROM metrics;
```

### CTEs (Common Table Expressions)

```sql
-- ✅ CTE pour lisibilité
WITH active_users AS (
    SELECT user_id, name, email
    FROM users
    WHERE is_active = TRUE
),
recent_orders AS (
    SELECT user_id, COUNT(*) AS order_count
    FROM orders
    WHERE order_date >= DATE_SUB(NOW(), INTERVAL 30 DAY)
    GROUP BY user_id
)
SELECT
    au.name,
    au.email,
    COALESCE(ro.order_count, 0) AS recent_orders
FROM active_users au
LEFT JOIN recent_orders ro ON au.user_id = ro.user_id
ORDER BY recent_orders DESC;

-- CTE récursive
WITH RECURSIVE category_tree AS (
    -- Cas de base
    SELECT id, name, parent_id, 1 AS level
    FROM categories
    WHERE parent_id IS NULL

    UNION ALL

    -- Cas récursif
    SELECT c.id, c.name, c.parent_id, ct.level + 1
    FROM categories c
    INNER JOIN category_tree ct ON c.parent_id = ct.id
)
SELECT * FROM category_tree;
```

---

## 📊 Vues et Procédures

### Vues

```sql
-- Créer une vue
CREATE VIEW active_user_summary AS
SELECT
    u.user_id,
    u.name,
    u.email,
    COUNT(o.order_id) AS total_orders,
    SUM(o.total_amount) AS lifetime_value
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
WHERE u.is_active = TRUE
GROUP BY u.user_id, u.name, u.email;

-- Utiliser la vue
SELECT * FROM active_user_summary
WHERE total_orders > 5;

-- Vue matérialisée (selon SGBD)
CREATE MATERIALIZED VIEW monthly_sales AS
SELECT
    DATE_FORMAT(order_date, '%Y-%m') AS month,
    SUM(total_amount) AS total_sales
FROM orders
GROUP BY month;
```

### Procédures stockées

```sql
-- Créer une procédure
DELIMITER //

CREATE PROCEDURE GetUserOrders(IN userId INT)
BEGIN
    SELECT
        o.order_id,
        o.order_date,
        o.total_amount
    FROM orders o
    WHERE o.user_id = userId
    ORDER BY o.order_date DESC;
END //

DELIMITER ;

-- Appeler la procédure
CALL GetUserOrders(123);

-- Procédure avec logique
DELIMITER //

CREATE PROCEDURE CreateOrder(
    IN p_user_id INT,
    IN p_total_amount DECIMAL(10,2),
    OUT p_order_id INT
)
BEGIN
    START TRANSACTION;

    INSERT INTO orders (user_id, total_amount, order_date)
    VALUES (p_user_id, p_total_amount, NOW());

    SET p_order_id = LAST_INSERT_ID();

    UPDATE users
    SET total_spent = total_spent + p_total_amount
    WHERE user_id = p_user_id;

    COMMIT;
END //

DELIMITER ;
```

---

## 🧪 Testing et Debugging

### EXPLAIN pour analyser les requêtes

```sql
-- Analyser le plan d'exécution
EXPLAIN SELECT *
FROM users u
JOIN orders o ON u.user_id = o.user_id
WHERE u.email = 'test@example.com';

-- Format étendu
EXPLAIN ANALYZE
SELECT * FROM products
WHERE price > 100;
```

### Profiling

```sql
-- Activer le profiling
SET profiling = 1;

-- Exécuter des requêtes
SELECT * FROM users WHERE email = 'test@example.com';

-- Voir les profils
SHOW PROFILES;

-- Détails d'une requête
SHOW PROFILE FOR QUERY 1;
```

---

## 📝 Commentaires

```sql
-- Commentaire sur une ligne

/*
 * Commentaire sur
 * plusieurs lignes
 */

-- Documenter les requêtes complexes
-- Cette requête calcule le chiffre d'affaires mensuel
-- en excluant les commandes annulées
SELECT
    DATE_FORMAT(order_date, '%Y-%m') AS month,
    SUM(total_amount) AS revenue
FROM orders
WHERE status != 'cancelled'
GROUP BY month
ORDER BY month DESC;
```

---

## 📚 Ressources

- [MySQL Documentation](https://dev.mysql.com/doc/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [SQL Style Guide](https://www.sqlstyle.guide/)
- [Use The Index, Luke](https://use-the-index-luke.com/)
- [Database Design Best Practices](https://www.guru99.com/database-design.html)
