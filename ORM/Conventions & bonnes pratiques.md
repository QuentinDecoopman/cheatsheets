# Conventions & Bonnes Pratiques ORM (Universelles)

## 📋 Principes Généraux

### Qu'est-ce qu'un ORM ?

Object-Relational Mapping (ORM) est une technique de programmation qui permet de convertir des données entre des systèmes de types incompatibles (objets ↔ base de données relationnelle).

**Avantages:**

- Abstraction de la base de données
- Code plus maintenable et lisible
- Protection contre les injections SQL
- Migrations de schéma facilitées
- Support multi-SGBD

**Inconvénients:**

- Overhead de performance
- Courbe d'apprentissage
- Abstraction peut masquer des problèmes
- Requêtes complexes parfois difficiles

---

## 🎯 Conventions de Nommage Universelles

### Modèles/Entities

```typescript
// ✅ Bon - PascalCase, singulier
class User {}
class Product {}
class OrderItem {}
class UserProfile {}

// ❌ Éviter
class users {}
class PRODUCT {}
class order_items {}
class userprofile {}
```

### Tables en Base de Données

```typescript
// ✅ Bon - snake_case, pluriel
// User → users
// Product → products
// OrderItem → order_items
// UserProfile → user_profiles

// Configuration personnalisée selon ORM:
// TypeORM: @Entity({ name: 'user_accounts' })
// Prisma: @@map("user_accounts")
// Sequelize: tableName: 'user_accounts'
```

### Propriétés/Colonnes

```typescript
// ✅ Bon - camelCase dans le code
class User {
  id: number;
  firstName: string;
  lastName: string;
  emailAddress: string;
  isActive: boolean;
  createdAt: Date;
}

// ❌ Éviter
class User {
  ID: number;
  first_name: string;
  LastName: string;
  email_address: string;
}
```

### Mapping Code ↔ BDD

```
Code (camelCase) → BDD (snake_case)

firstName    → first_name
emailAddress → email_address
isActive     → is_active
createdAt    → created_at
userId       → user_id
```

---

## 🔑 Clés Primaires

### Auto-increment

```typescript
// ✅ Bon pour applications centralisées
// TypeORM
@PrimaryGeneratedColumn()
id: number;

// Prisma
id Int @id @default(autoincrement())

// Sequelize
id: {
  type: DataTypes.INTEGER,
  autoIncrement: true,
  primaryKey: true,
}
```

### UUID

```typescript
// ✅ Bon pour systèmes distribués, APIs publiques
// TypeORM
@PrimaryGeneratedColumn('uuid')
id: string;

// Prisma
id String @id @default(uuid())

// Sequelize
id: {
  type: DataTypes.UUID,
  defaultValue: DataTypes.UUIDV4,
  primaryKey: true,
}

// Avantages: Non prédictible, unique globalement
// Inconvénients: Plus lourd, index moins performants
```

---

## 🔗 Relations

### One-to-Many (1:N)

**Règle:** La clé étrangère se trouve du côté "Many"

```typescript
// Un User a plusieurs Posts
// Un Post appartient à un User

User {
  id
  posts[]  // Relation virtuelle
}

Post {
  id
  authorId  // Foreign Key
  author    // Relation
}
```

### Many-to-Many (N:N)

**Règle:** Table de jonction avec clés étrangères des deux côtés

```typescript
// Plusieurs Users ont plusieurs Roles
// Plusieurs Roles ont plusieurs Users

User {
  id
  userRoles[]  // Via table de jonction
}

Role {
  id
  userRoles[]  // Via table de jonction
}

UserRole {
  userId   // FK
  roleId   // FK
  // Champs additionnels possibles
  assignedAt
}
```

### One-to-One (1:1)

**Règle:** Clé étrangère unique d'un côté

```typescript
// Un User a un Profile
// Un Profile appartient à un User

User {
  id
  profile  // Relation
}

Profile {
  id
  userId   // FK UNIQUE
  user     // Relation
}
```

---

## 🏗️ Structure des Modèles

### Champs standards

```typescript
// ✅ Recommandé pour tous les modèles
class BaseEntity {
  id: number | string; // PK
  createdAt: Date; // Date de création
  updatedAt: Date; // Date de dernière modification
  deletedAt?: Date | null; // Soft delete (optionnel)
}

class User extends BaseEntity {
  email: string;
  // ... autres champs
}
```

### Types de données cohérents

```typescript
// ✅ Bon - types appropriés
class Product {
  id: number;
  name: string; // VARCHAR
  description: string; // TEXT
  price: Decimal; // DECIMAL(10,2) pour argent
  stock: number; // INTEGER
  isActive: boolean; // BOOLEAN
  rating: number; // DECIMAL(2,1) ou FLOAT
  metadata: object; // JSON/JSONB
  createdAt: Date; // TIMESTAMP
}

// ❌ Éviter
class Product {
  id: string; // INTEGER en string
  price: number; // FLOAT pour argent (imprécis!)
  isActive: string; // 'true'/'false' en string
  createdAt: string; // Date en string
}
```

---

## ✅ Bonnes Pratiques Universelles

### 1. N+1 Problem - À ÉVITER ABSOLUMENT

```typescript
// ❌ MAUVAIS - N+1 queries
const users = await findAllUsers();
for (const user of users) {
  // 1 query par user = N queries supplémentaires!
  user.posts = await findPostsByUserId(user.id);
}

// ✅ BON - Eager loading
const users = await findAllUsersWithPosts();
// 1 seule query (ou 2 max avec JOIN)
```

### 2. Select minimal

```typescript
// ❌ Éviter - récupère TOUT
const users = await findAll();

// ✅ Bon - seulement les champs nécessaires
const users = await findAll({
  select: ["id", "email", "firstName"],
});
```

### 3. Pagination obligatoire

```typescript
// ❌ Dangereux - peut retourner des millions de lignes
const users = await findAll();

// ✅ Bon - Offset pagination (simple)
const users = await findAll({
  limit: 20,
  offset: page * 20,
});

// ✅ Meilleur - Cursor pagination (performances)
const users = await findAll({
  limit: 20,
  cursor: lastId,
  orderBy: "id",
});
```

### 4. Index sur les colonnes fréquentes

```sql
-- Colonnes à indexer:
-- ✅ Foreign keys
-- ✅ Colonnes dans WHERE
-- ✅ Colonnes dans ORDER BY
-- ✅ Colonnes UNIQUE
-- ✅ Colonnes pour JOIN

CREATE INDEX idx_posts_author_id ON posts(author_id);
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_created_at ON orders(created_at);
CREATE INDEX idx_products_category_status ON products(category_id, status);
```

### 5. Transactions pour opérations multiples

```typescript
// ✅ Bon - Tout ou rien
await transaction(async (tx) => {
  const user = await tx.createUser(data);
  await tx.createProfile({ userId: user.id });
  await tx.createSettings({ userId: user.id });
  // Si erreur → rollback automatique
});

// ❌ Éviter - Risque d'incohérence
const user = await createUser(data);
await createProfile({ userId: user.id }); // Peut échouer!
await createSettings({ userId: user.id }); // Peut échouer!
```

### 6. Validation des données

```typescript
// ✅ Valider AVANT d'enregistrer
class User {
  @IsEmail()
  email: string;

  @MinLength(8)
  password: string;

  @Min(18)
  @Max(120)
  age: number;
}

// Valider
const errors = await validate(user);
if (errors.length > 0) {
  throw new ValidationError(errors);
}
```

### 7. Soft Delete plutôt que Hard Delete

```typescript
// ✅ Soft delete - données préservées
class User {
  id: number;
  email: string;
  deletedAt: Date | null; // NULL = actif, Date = supprimé
}

// Requêtes
const activeUsers = await find({
  where: { deletedAt: null },
});

// Soft delete
await update(userId, { deletedAt: new Date() });

// Restaurer
await update(userId, { deletedAt: null });

// ❌ Hard delete - données perdues
await delete userId; // Utiliser seulement si vraiment nécessaire
```

### 8. Migrations versionnées

```bash
# ✅ Bon - Migrations avec noms explicites
20240125_create_users_table.sql
20240126_add_email_index_to_users.sql
20240127_create_posts_table.sql

# Workflow:
# 1. Créer migration
# 2. Tester en dev
# 3. Review
# 4. Appliquer en staging
# 5. Appliquer en production

# ❌ Éviter
# Modifier directement la base en production
# Supprimer/modifier des migrations déjà appliquées
```

### 9. Nommer les contraintes

```sql
-- ✅ Bon - noms explicites
CONSTRAINT pk_users_id PRIMARY KEY (id)
CONSTRAINT fk_posts_author_id FOREIGN KEY (author_id) REFERENCES users(id)
CONSTRAINT uk_users_email UNIQUE (email)
CONSTRAINT chk_products_price CHECK (price >= 0)

-- Convention:
-- pk_ : Primary Key
-- fk_ : Foreign Key
-- uk_ : Unique Key
-- chk_ : Check constraint
-- idx_ : Index
```

### 10. Utiliser les timestamps

```typescript
// ✅ Bon - tracking automatique
class BaseEntity {
  createdAt: Date; // Auto-set à la création
  updatedAt: Date; // Auto-update à chaque modification
}

// Permet de:
// - Auditer les changements
// - Trier par date
// - Filtrer par période
// - Détecter les données obsolètes
```

---

## 🚫 Anti-Patterns à Éviter

### 1. God Models

```typescript
// ❌ Mauvais - modèle avec 50+ colonnes
class User {
  // 50+ propriétés
  // Beaucoup de champs rarement utilisés
}

// ✅ Bon - séparation logique
class User {
  id: number;
  email: string;
  profile: UserProfile; // Relation
  settings: UserSettings; // Relation
  preferences: UserPrefs; // Relation
}
```

### 2. Modèles anémiques

```typescript
// ❌ Mauvais - juste un conteneur de données
class User {
  id: number;
  email: string;
  // Aucune logique métier
}

// ✅ Bon - logique métier encapsulée
class User {
  id: number;
  email: string;

  isAdmin(): boolean {
    return this.role === "ADMIN";
  }

  canEditPost(post: Post): boolean {
    return this.id === post.authorId || this.isAdmin();
  }
}
```

### 3. Logique métier dans les requêtes

```typescript
// ❌ Mauvais - logique éparpillée
const user = await findUser(id);
if (user.role === "ADMIN" || user.permissions.includes("EDIT")) {
  // ...
}

// ✅ Bon - encapsulée dans le modèle
const user = await findUser(id);
if (user.canEdit()) {
  // ...
}
```

### 4. Requêtes SQL brutes partout

```typescript
// ❌ Éviter (sauf cas complexes)
const users = await query("SELECT * FROM users WHERE email = ?", [email]);

// ✅ Utiliser l'ORM
const users = await userRepository.find({ where: { email } });

// ✅ Raw queries seulement pour cas complexes
const stats = await queryRaw(`
  SELECT 
    date_trunc('month', created_at) as month,
    COUNT(*) as user_count
  FROM users
  GROUP BY month
`);
```

---

## 📊 Comparaison ORM Populaires

| Caractéristique          | TypeORM    | Prisma      | Sequelize     |
| ------------------------ | ---------- | ----------- | ------------- |
| **Langage**              | TypeScript | Schema DSL  | JavaScript/TS |
| **Type Safety**          | ⭐⭐⭐     | ⭐⭐⭐⭐⭐  | ⭐⭐          |
| **Migrations**           | ✅         | ✅          | ✅            |
| **Active Record**        | ✅         | ❌          | ❌            |
| **Data Mapper**          | ✅         | ✅          | ✅            |
| **GUI**                  | ❌         | ✅ (Studio) | ❌            |
| **Performance**          | ⭐⭐⭐     | ⭐⭐⭐⭐    | ⭐⭐⭐        |
| **Courbe apprentissage** | Moyenne    | Facile      | Moyenne       |

---

## 🔐 Sécurité

### 1. Jamais de SQL raw avec interpolation

```typescript
// ❌ DANGEREUX - Injection SQL!
const users = await query(`SELECT * FROM users WHERE email = '${email}'`);

// ✅ Paramètres préparés
const users = await query("SELECT * FROM users WHERE email = ?", [email]);
```

### 2. Valider les entrées utilisateur

```typescript
// ✅ Toujours valider
function createUser(data: any) {
  // Validation
  if (!isEmail(data.email)) {
    throw new Error("Invalid email");
  }

  if (data.age < 18 || data.age > 120) {
    throw new Error("Invalid age");
  }

  return userRepository.create(data);
}
```

### 3. Masquer les données sensibles

```typescript
class User {
  id: number;
  email: string;
  password: string;

  // ✅ Ne jamais sérialiser le mot de passe
  toJSON() {
    const { password, ...user } = this;
    return user;
  }
}
```

---

## 📚 Ressources

### ORMs Populaires

- **[TypeORM](https://typeorm.io/)** - ORM TypeScript complet
- **[Prisma](https://www.prisma.io/)** - ORM moderne avec type-safety
- **[Sequelize](https://sequelize.org/)** - ORM JavaScript/TypeScript mature
- **[MikroORM](https://mikro-orm.io/)** - TypeScript ORM basé sur Data Mapper
- **[Mongoose](https://mongoosejs.com/)** - ODM pour MongoDB

### Documentation Générale

- [Database Design Patterns](https://www.databaseanswers.org/data_models/)
- [OWASP - Database Security](https://cheatsheetseries.owasp.org/cheatsheets/Database_Security_Cheat_Sheet.html)
- [SQL Anti-Patterns](https://pragprog.com/titles/bksqla/sql-antipatterns/)
