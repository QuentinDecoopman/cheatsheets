# Conventions & Bonnes Pratiques - Conception BDD

## 📋 Principes Généraux

### Règles de normalisation

```
1NF (Première Forme Normale)
- Valeurs atomiques (pas de listes)
- Chaque colonne contient une seule valeur
- Pas de groupes répétitifs

2NF (Deuxième Forme Normale)
- Respecte 1NF
- Pas de dépendance partielle
- Chaque attribut non-clé dépend de toute la clé primaire

3NF (Troisième Forme Normale)
- Respecte 2NF
- Pas de dépendance transitive
- Chaque attribut non-clé dépend directement de la clé primaire

BCNF (Boyce-Codd)
- Respecte 3NF
- Chaque déterminant est une clé candidate
```

---

## 🎯 Conventions de Nommage

### Tables

```sql
-- ✅ Bon - noms au pluriel, snake_case
users
products
order_items
user_addresses

-- ❌ Éviter
User
tblUsers
UserAddress
```

### Colonnes

```sql
-- ✅ Bon - snake_case, descriptif
user_id
first_name
created_at
is_active

-- ❌ Éviter
userId
FName
CreatedDate
active
```

### Clés primaires

```sql
-- ✅ Option 1 - id (simple)
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255)
);

-- ✅ Option 2 - table_id (explicite)
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    email VARCHAR(255)
);

-- ✅ UUID pour systèmes distribués
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255)
);
```

### Clés étrangères

```sql
-- ✅ Bon - référence explicite
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    created_at TIMESTAMP
);

-- Convention de nommage FK
ALTER TABLE orders
ADD CONSTRAINT fk_orders_user_id
FOREIGN KEY (user_id) REFERENCES users(id);
```

### Index

```sql
-- Convention: idx_table_column
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_products_category_name ON products(category, name);

-- Index unique: uk_table_column
CREATE UNIQUE INDEX uk_users_email ON users(email);
```

---

## 🏗️ Modélisation des Relations

### One-to-Many (1:N)

```sql
-- Exemple: Un utilisateur a plusieurs commandes
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    total_amount DECIMAL(10,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Many-to-Many (N:N)

```sql
-- Exemple: Produits et catégories
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    price DECIMAL(10,2)
);

CREATE TABLE categories (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL
);

-- Table de jonction
CREATE TABLE product_categories (
    product_id INTEGER REFERENCES products(id) ON DELETE CASCADE,
    category_id INTEGER REFERENCES categories(id) ON DELETE CASCADE,
    PRIMARY KEY (product_id, category_id)
);
```

### One-to-One (1:1)

```sql
-- Exemple: Utilisateur et profil
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE
);

CREATE TABLE user_profiles (
    user_id INTEGER PRIMARY KEY REFERENCES users(id) ON DELETE CASCADE,
    bio TEXT,
    avatar_url VARCHAR(500),
    date_of_birth DATE
);
```

### Héritage (Stratégies)

```sql
-- Stratégie 1: Table par hiérarchie (Single Table Inheritance)
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    type VARCHAR(50) NOT NULL, -- 'manager', 'developer', 'designer'
    name VARCHAR(255) NOT NULL,
    salary DECIMAL(10,2),
    -- Champs spécifiques (peuvent être NULL)
    team_size INTEGER,          -- Pour managers
    programming_language VARCHAR(50), -- Pour developers
    design_tool VARCHAR(50)     -- Pour designers
);

-- Stratégie 2: Table par type (Class Table Inheritance)
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    salary DECIMAL(10,2)
);

CREATE TABLE managers (
    id INTEGER PRIMARY KEY REFERENCES employees(id),
    team_size INTEGER
);

CREATE TABLE developers (
    id INTEGER PRIMARY KEY REFERENCES employees(id),
    programming_language VARCHAR(50)
);

-- Stratégie 3: Table par classe concrète
CREATE TABLE managers (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    salary DECIMAL(10,2),
    team_size INTEGER
);

CREATE TABLE developers (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    salary DECIMAL(10,2),
    programming_language VARCHAR(50)
);
```

---

## 🔑 Choix des Clés Primaires

### Auto-increment (SERIAL)

```sql
-- ✅ Pour applications simples, centralisées
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255)
);

-- Avantages: Simple, performant, léger
-- Inconvénients: Prédictible, problèmes en distribué
```

### UUID

```sql
-- ✅ Pour systèmes distribués, APIs publiques
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255)
);

-- Avantages: Unique globalement, non prédictible
-- Inconvénients: Plus lourd, moins performant pour index
```

### Composite Keys

```sql
-- ✅ Pour tables de jonction
CREATE TABLE user_roles (
    user_id INTEGER REFERENCES users(id),
    role_id INTEGER REFERENCES roles(id),
    granted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (user_id, role_id)
);
```

---

## 📐 Types de Données

### Choix appropriés

```sql
-- ✅ Bon
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,          -- Limite raisonnable
    description TEXT,                     -- Texte long
    price DECIMAL(10,2) NOT NULL,        -- Précision pour argent
    stock INTEGER DEFAULT 0,             -- Quantité
    is_active BOOLEAN DEFAULT true,      -- Oui/Non
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- ❌ Éviter
CREATE TABLE products (
    id VARCHAR(255),                      -- Surdimensionné
    name TEXT,                            -- Pas de limite
    price FLOAT,                          -- Imprécis pour argent
    stock VARCHAR(10),                    -- Type incorrect
    is_active VARCHAR(5),                 -- 'true'/'false' en string
    created_at VARCHAR(50)                -- Date en string
);
```

### Types PostgreSQL

```sql
-- Numériques
INTEGER, BIGINT, DECIMAL(p,s), NUMERIC(p,s)
REAL, DOUBLE PRECISION

-- Texte
VARCHAR(n), TEXT, CHAR(n)

-- Date/Heure
DATE, TIME, TIMESTAMP, TIMESTAMPTZ, INTERVAL

-- Booléen
BOOLEAN

-- JSON
JSON, JSONB (indexable, performant)

-- Tableaux
INTEGER[], TEXT[]

-- UUID
UUID

-- Géométrie
POINT, LINE, POLYGON
```

---

## 🔒 Contraintes et Validations

### NOT NULL

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    nickname VARCHAR(100)  -- Optionnel
);
```

### UNIQUE

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE,
    username VARCHAR(50) NOT NULL UNIQUE
);

-- Ou avec contrainte nommée
ALTER TABLE users
ADD CONSTRAINT uk_users_email UNIQUE (email);
```

### CHECK

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    price DECIMAL(10,2) CHECK (price >= 0),
    stock INTEGER CHECK (stock >= 0),
    rating DECIMAL(2,1) CHECK (rating >= 0 AND rating <= 5)
);

-- Contraintes nommées
ALTER TABLE products
ADD CONSTRAINT chk_products_price CHECK (price >= 0);
```

### DEFAULT

```sql
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    status VARCHAR(20) DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_paid BOOLEAN DEFAULT false,
    quantity INTEGER DEFAULT 1
);
```

### Foreign Key avec actions

```sql
CREATE TABLE order_items (
    id SERIAL PRIMARY KEY,
    order_id INTEGER REFERENCES orders(id) ON DELETE CASCADE,
    product_id INTEGER REFERENCES products(id) ON DELETE RESTRICT,
    quantity INTEGER NOT NULL
);

-- Options:
-- ON DELETE CASCADE    - Supprime les enfants
-- ON DELETE SET NULL   - Met NULL dans la FK
-- ON DELETE RESTRICT   - Empêche la suppression
-- ON DELETE NO ACTION  - Comme RESTRICT
-- ON UPDATE CASCADE    - Propage les modifications
```

---

## 🎨 Patterns Courants

### Soft Delete

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) NOT NULL,
    deleted_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Requêtes
SELECT * FROM users WHERE deleted_at IS NULL;  -- Actifs
UPDATE users SET deleted_at = NOW() WHERE id = 1;  -- Soft delete
```

### Audit Trail

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    price DECIMAL(10,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_by INTEGER REFERENCES users(id),
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_by INTEGER REFERENCES users(id)
);

-- Trigger pour updated_at
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER update_products_updated_at
BEFORE UPDATE ON products
FOR EACH ROW
EXECUTE FUNCTION update_updated_at_column();
```

### Versioning

```sql
CREATE TABLE documents (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    content TEXT,
    version INTEGER DEFAULT 1,
    is_current BOOLEAN DEFAULT true
);

-- Créer nouvelle version
INSERT INTO documents (title, content, version)
SELECT title, content, version + 1
FROM documents
WHERE id = 1 AND is_current = true;

UPDATE documents SET is_current = false WHERE id = 1;
```

### Tree Structure (Adjacency List)

```sql
CREATE TABLE categories (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    parent_id INTEGER REFERENCES categories(id),
    level INTEGER DEFAULT 0
);

-- Query descendants (récursif)
WITH RECURSIVE category_tree AS (
    SELECT id, name, parent_id, 0 as level
    FROM categories
    WHERE parent_id IS NULL

    UNION ALL

    SELECT c.id, c.name, c.parent_id, ct.level + 1
    FROM categories c
    INNER JOIN category_tree ct ON c.parent_id = ct.id
)
SELECT * FROM category_tree;
```

### Polymorphic Associations

```sql
-- Approche 1: Colonnes nullables (simple)
CREATE TABLE comments (
    id SERIAL PRIMARY KEY,
    content TEXT NOT NULL,
    user_id INTEGER REFERENCES users(id),
    post_id INTEGER REFERENCES posts(id) NULL,
    photo_id INTEGER REFERENCES photos(id) NULL,
    CHECK ((post_id IS NOT NULL)::INTEGER + (photo_id IS NOT NULL)::INTEGER = 1)
);

-- Approche 2: Table de jonction avec type
CREATE TABLE comments (
    id SERIAL PRIMARY KEY,
    content TEXT NOT NULL,
    user_id INTEGER REFERENCES users(id),
    commentable_type VARCHAR(50) NOT NULL,
    commentable_id INTEGER NOT NULL
);

-- Meilleure approche: Tables séparées
CREATE TABLE post_comments (
    id SERIAL PRIMARY KEY,
    post_id INTEGER REFERENCES posts(id),
    content TEXT NOT NULL
);

CREATE TABLE photo_comments (
    id SERIAL PRIMARY KEY,
    photo_id INTEGER REFERENCES photos(id),
    content TEXT NOT NULL
);
```

---

## 📊 Index et Performance

### Index simples

```sql
-- Index sur colonnes fréquemment recherchées
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_created_at ON orders(created_at);
CREATE INDEX idx_products_category_id ON products(category_id);
```

### Index composites

```sql
-- Ordre important: colonnes les plus sélectives en premier
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
CREATE INDEX idx_products_category_active ON products(category_id, is_active);
```

### Index partiels

```sql
-- Index seulement sur les enregistrements actifs
CREATE INDEX idx_users_active_email ON users(email)
WHERE deleted_at IS NULL;

-- Index sur commandes récentes
CREATE INDEX idx_orders_recent ON orders(created_at)
WHERE created_at > NOW() - INTERVAL '30 days';
```

### Index sur expressions

```sql
-- Index sur email en minuscules
CREATE INDEX idx_users_email_lower ON users(LOWER(email));

-- Index sur année de création
CREATE INDEX idx_orders_year ON orders(EXTRACT(YEAR FROM created_at));
```

---

## 🔐 Sécurité

### Permissions

```sql
-- Créer utilisateur application
CREATE USER app_user WITH PASSWORD 'secure_password';

-- Accorder permissions minimales
GRANT CONNECT ON DATABASE mydb TO app_user;
GRANT USAGE ON SCHEMA public TO app_user;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app_user;
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO app_user;

-- Read-only user
CREATE USER readonly_user WITH PASSWORD 'password';
GRANT CONNECT ON DATABASE mydb TO readonly_user;
GRANT USAGE ON SCHEMA public TO readonly_user;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly_user;
```

### Row Level Security (PostgreSQL)

```sql
-- Activer RLS
ALTER TABLE documents ENABLE ROW LEVEL SECURITY;

-- Politique: utilisateurs voient uniquement leurs documents
CREATE POLICY user_documents ON documents
FOR ALL
TO app_user
USING (user_id = current_user_id());
```

---

## 📚 Ressources

- [Database Normalization](https://en.wikipedia.org/wiki/Database_normalization)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [MySQL Best Practices](https://dev.mysql.com/doc/refman/8.0/en/optimization.html)
- [Database Design Patterns](https://www.databaseanswers.org/data_models/)
