# Conventions & Bonnes Pratiques Prisma

## 📋 Introduction à Prisma

Prisma est un ORM moderne pour Node.js et TypeScript qui simplifie l'accès aux bases de données avec une approche type-safe et intuitive.

**Avantages de Prisma:**

- Type-safety complète avec TypeScript
- Schema déclaratif facile à lire
- Migrations automatiques
- Prisma Client généré automatiquement
- Prisma Studio pour visualiser les données
- Support PostgreSQL, MySQL, SQLite, MongoDB, SQL Server

**Composants:**

- **Prisma Schema** : Définition du modèle de données
- **Prisma Client** : Client généré pour les requêtes
- **Prisma Migrate** : Gestion des migrations
- **Prisma Studio** : Interface graphique

---

## 🎯 Conventions de Nommage

### Modèles

```prisma
// ✅ Bon - PascalCase, singulier
model User {}
model Product {}
model OrderItem {}

// ❌ Éviter
model users {}
model PRODUCT {}
model order_items {}
```

### Tables (@@map)

```prisma
// ✅ Par défaut - snake_case pluriel automatique
model User {
  id Int @id
  // Table générée: "User" (peut être configuré)
}

// Configuration personnalisée
model User {
  id Int @id
  @@map("users")  // Table: users
}

model OrderItem {
  id Int @id
  @@map("order_items")
}
```

### Champs (Colonnes)

```prisma
// ✅ Bon - camelCase dans le schema
model User {
  id        Int    @id
  firstName String
  lastName  String
  email     String
}

// Mapping vers snake_case en BDD avec @map
model User {
  id        Int    @id @default(autoincrement())
  firstName String @map("first_name")
  lastName  String @map("last_name")
  email     String @unique

  @@map("users")
}
```

---

## 🏗️ Définition des Modèles

### Modèle complet

```prisma
// schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        Int       @id @default(autoincrement())
  email     String    @unique @db.VarChar(255)
  password  String    @db.VarChar(255)
  firstName String    @map("first_name") @db.VarChar(100)
  lastName  String    @map("last_name") @db.VarChar(100)
  isActive  Boolean   @default(true) @map("is_active")
  role      Role      @default(USER)
  createdAt DateTime  @default(now()) @map("created_at")
  updatedAt DateTime  @updatedAt @map("updated_at")
  deletedAt DateTime? @map("deleted_at")

  posts     Post[]
  profile   Profile?

  @@map("users")
  @@index([email])
  @@index([createdAt])
}

enum Role {
  USER
  ADMIN
  MODERATOR
}
```

### Types de champs

```prisma
model Example {
  // Nombres
  id          Int       @id @default(autoincrement())
  bigNumber   BigInt
  floatNum    Float
  decimalNum  Decimal

  // Texte
  name        String    @db.VarChar(255)
  description String    @db.Text

  // Booléen
  isActive    Boolean   @default(true)

  // Dates
  createdAt   DateTime  @default(now())
  updatedAt   DateTime  @updatedAt
  birthDate   DateTime? @db.Date

  // JSON
  metadata    Json

  // Enum
  status      Status    @default(PENDING)

  // UUID
  uuid        String    @default(uuid()) @db.Uuid

  // Bytes
  avatar      Bytes?
}

enum Status {
  PENDING
  ACTIVE
  INACTIVE
}
```

### Contraintes et validations

```prisma
model Product {
  id          Int      @id @default(autoincrement())
  name        String   @db.VarChar(255)
  sku         String   @unique
  price       Decimal  @db.Decimal(10, 2)
  stock       Int      @default(0)
  description String?  @db.Text

  // Contraintes composées
  @@unique([name, sku])
  @@index([price])

  @@map("products")
}
```

---

## 🔗 Relations

### One-to-Many

```typescript
// TypeORM
@Entity('users')
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @OneToMany(() => Post, post => post.author)
  posts: Post[];
}

@Entity('posts')
export class Post {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  title: string;

  @ManyToOne(() => User, user => user.posts, { onDelete: 'CASCADE' })
  @JoinColumn({ name: 'user_id' })
  author: User;

  @Column({ name: 'user_id' })
  userId: number;
}

// Prisma
model User {
  id    Int    @id @default(autoincrement())
  posts Post[]
}

model Post {
  iprisma
model User {
  id    Int    @id @default(autoincrement())
  email String @unique
  posts Post[]

  @@map("users")
}

model Post {
  id       Int      @id @default(autoincrement())
  title    String
  content  String   @db.Text
  author   User     @relation(fields: [authorId], references: [id], onDelete: Cascade)
  authorId Int      @map("author_id")

  @@map("posts")
  @@index([authorId])
}
```

### Many-to-Many (Implicite)

```prisma
// ✅ Relation implicite (recommandé si pas de champs additionnels)
model Post {
  id         Int        @id @default(autoincrement())
  title      String
  categories Category[]

  @@map("posts")
}

model Category {
  id    Int    @id @default(autoincrement())
  name  String
  posts Post[]

  @@map("categories")
}

// Prisma génère automatiquement la table _CategoryToPost
```

### Many-to-Many (Explicite)

```prisma
// ✅ Relation explicite (si champs additionnels nécessaires)
model User {
  id        Int        @id @default(autoincrement())
  email     String     @unique
  userRoles UserRole[]

  @@map("users")
}

model Role {
  id        Int        @id @default(autoincrement())
  name      String     @unique
  userRoles UserRole[]

  @@map("roles")
}

model UserRole {
  user       User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  userId     Int      @map("user_id")
  role       Role     @relation(fields: [roleId], references: [id], onDelete: Cascade)
  roleId     Int      @map("role_id")
  assignedAt DateTime @default(now()) @map("assigned_at")
  assignedBy String?  @map("assigned_by")

  @@id([userId, roleId])
  @@map("user_roles")
}
```

### One-to-One

```prisma
model User {
  id      Int      @id @default(autoincrement())
  email   String   @unique
  profile Profile?

  @@map("users")
}

model Profile {
  id        Int     @id @default(autoincrement())
  bio       String? @db.Text
  avatarUrl String? @map("avatar_url")
  user      User    @relation(fields: [userId], references: [id], onDelete: Cascade)
  userId    Int     @unique @map("user_id")

  @@map("profiles")
}
```

### Self-relations

````prisma
// Hiérarchie (arbre)
model Category {
  id       Int        @id @default(autoincrement())
  name     String
  parent   Category?  @relation("CategoryTree", fields: [parentId], references: [id])
  parentId Int?       @map("parent_id")
  children Category[] @relation("CategoryTree")

  @@map("categories")
}

// Amis (bidirectionnel)
model User {
  id          Int    @id @default(autoincrement())
  friendships Friendship[] @relation("UserFriendships")
  friendsOf   Friendship[] @relation("FriendOf")

  @@map("users")
}

model Friendship {
  user     User @relation("UserFriendships", fields: [userId], references: [id])
  userId   Int  @map("user_id")
  friend   User @relation("FriendOf", fields: [friendId], references: [id])
  friendId Int  @map("friend_id")

  @@id([userId, friendId])
  @@map("friendships

// Prisma
const user = await prisma.user.findUnique({
  where: { id: 1 },
  include: {
    posts: true,
    profile: t Prisma Client

### Requêtes de base

```typescript
// Tous les enregistrements
const users = await prisma.user.findMany();

// Un enregistrement unique
const user = await prisma.user.findUnique({
  where: { id: 1 },
});

// Premier résultat correspondant
const user = await prisma.user.findFirst({
  where: { email: "john@example.com" },
});

// Ou null si non trouvé
const user = await prisma.user.findUnique({
  where: { id: 999 },
});

// Ou erreur si non trouvé
const user = await prisma.user.findUniqueOrThrow({
  where: { id: 999 },
});
````

### Filtrage

````typescript
// Conditions simples
const activeUsers = await prisma.user.findMany({
  where: {
    isActive: true,
  },
});

// Opérateurs
const users = await prisma.user.findMany({
  where: {
    age: { gte: 18, lte: 65 },  // >= 18 ET <= 65
    email: { contains: "@gmail.com" },
    name: { startsWith: "John" },
    createdAt: { gt: new Date("2023-01-01") },
  },
});

// Opérateurs: gt, gte, lt, lte, contains, startsWith, endsWith, not, in, notIn

// Conditions multiples (AND)
const users = await prisma.user.findMany({
  where: {
    isActive: true,
    role: "ADMIN",
  },
});

// ORMutations (Create, Update, Delete)

### Create

```typescript
// Créer un seul enregistrement
const user = await prisma.user.create({
  data: {
    email: "john@example.com",
    firstName: "John",
    lastName: "Doe",
  },
});

// Créer avec relations
const user = await prisma.user.create({
  data: {
    email: "john@example.com",
    firstName: "John",
    profile: {
      create: {
        bio: "Hello World",
      },
    },
    posts: {
      create: [
        { title: "First Post", content: "Content 1" },
        { title: "Second Post", content: "Content 2" },
      ],
    },
  },
  include: {
    profile: true,
    posts: true,
  },
});

// Créer plusieurs (batch)
const users = await prisma.user.createMany({
  data: [
    { email: "user1@example.com", firstName: "User1" },
    { email: "user2@example.com", firstName: "User2" },
  ],
  skipDuplicates: true,  // Ignorer les doublons
});

// Connecter à des enregistrements existants
const post = await prisma.post.create({
  data: {
    title: "My Post",
    content: "Content",
    author: {
      connect: { id: 1 },  // Connecter à user existant
    },
    categories: {
      connect: [
        { id: 1 },
        { id: 2 },
      ],
    },
  },
});
````

### Update

````typescript
// Mettre à jour un enregistrement
const user = await prisma.user.update({
  where: { id: 1 },
  data: {
    fransaction interactive

```typescript
// ✅ Recommandé - rollback automatique en cas d'erreur
await prisma.$transaction(async (tx) => {
  // Toutes les opérations utilisent 'tx' au lieu de 'prisma'
  const user = await tx.user.create({
    data: {
      email: "john@example.com",
      firstName: "John",
    },
  });

  const profile = await tx.profile.create({
    data: {
      userId: user.id,
      bio: "Hello World",
    },
  });

  // Si une erreur est levée, tout est annulé
  if (someCondition) {
    throw new Error("Rollback transaction");
  }

  return { user, profile };
});
````

### Transaction séquentielle

````typescript
// Pour des opérations indépendantes
const [users, posts] = await prisma.$transaction([
  prismÉviter le problème N+1

```typescript
// ❌ Mauvais - N+1 queries (1 + N)
const users = await prisma.user.findMany();
for (const user of users) {
  user.posts = await prisma.post.findMany({ where: { authorId: user.id } });
}

// ✅ Bon - Une seule query avec include
const users = await prisma.user.findMany({
  include: { posts: true },
});

// ✅ Encore mieux - select seulement ce qui est nécessaire
const users = await prisma.user.findMany({
  select: {
    id: true,
    email: true,
    posts: {
      select: {
        id: true,
        title: true,
      },
    },
  },
});
````

### 2. Select vs Include

```typescript
// include - Récupère tout + relations
const users = await prisma.user.findMany({
  include: { posts: true },
});

// select - Récupère seulement les champs spécifiés
const users = await prisma.user.findMany({
  select: {
    id: true,
    email: true,
    firstName: true,
  },
});

// ✅ Bon - Combiner select sur les relations
const users = await prisma.user.findMany({
  select: {
    id: true,
    email: true,
    posts: {
      select: {
        id: true,
        title: true,
      },
      where: { published: true },
      take: 5,
    },
  },
});
```

### 3. Pagination efficace

````typescript
// ✅ Cursor-based (recommandé pour grandes données)
async function getPaginatedUsers(cursor?: number) {
  const users = await prisma.user.findMany({
    take: 10,
    ...(cursor && {
      skip: 1,  // Sauter le curseur
      cursor: { id: cursor },
    }),
    orderBy: { id: "asc" },
  });

  const lastUser = users[users.length - 1];
  const nextCursor = lastUser?.id;

  re�️ Migrations

### Créer une migration

```bash
# En développement - reset + apply
npx prisma migrate dev --name init

# Créer migration sans l'appliquer
npx prisma migrate dev --create-only

# En production - apply seulement
npx prisma migrate deploy
````

### Workflow de migration

```bash
# 1. Modifier schema.prisma
# 2. Créer la migration
npx prisma migrate dev --name add_user_role

# 3. Vérifier la migration générée
# prisma/migrations/20240125_add_user_role/migration.sql

# 4. Appliquer en production
npx prisma migrate deploy

# Status des migrations
npx prisma migrate status

# Reset (développement uniquement)
npx prisma migrate reset
```

### Prisma Studio

```bash
# Ouvrir l'interface graphique
npx prisma studio
```

---

## 📚 Ressources

- [Prisma Documentation](https://www.prisma.io/docs)
- [Prisma Schema Reference](https://www.prisma.io/docs/reference/api-reference/prisma-schema-reference)
- [Prisma Client API](https://www.prisma.io/docs/reference/api-reference/prisma-client-reference)
- [Prisma Examples](https://github.com/prisma/prisma-examples)
- [Prisma Data Guide](https://www.prisma.io/dataguidetion([
  prisma.user.findMany({
  skip: page \* pageSize,
  take: pageSize,
  }),
  prisma.user.count(),
  ]);

  return {
  users,
  total,
  page,
  totalPages: Math.ceil(total / pageSize),
  };
  }

````

### 4. Index pour la performance

```prisma
model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique  // Index automatique
  firstName String
  lastName  String
  createdAt DateTime @default(now())

  // Index simple
  @@index([email])

  // Index composite
  @@index([firstName, lastName])

  // Index sur dates (pour tri/filtrage)
  @@index([createdAt])

  @@map("users")
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  authorId  Int
  published Boolean  @default(false)
  createdAt DateTime @default(now())

  // Index pour foreign keys
  @@index([authorId])

  // Index composites fréquemment utilisés ensemble
  @@index([published, createdAt])

  @@map("posts")
}
````

### 5. Batch operations

```typescript
// ✅ Bon - Batch create
await prisma.user.createMany({
  data: users,
  skipDuplicates: true,
});

// ✅ Bon - Batch update
await prisma.user.updateMany({
  where: { isActive: false },
  data: { deletedAt: new Date() },
});

// ❌ Éviter - Boucle d'opérations individuelles
for (const user of users) {
  await prisma.user.create({ data: user }); // NON!
}
```

### 6. Connection pooling

```typescript
// prisma/schema.prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// .env
DATABASE_URL="postgresql://user:pass@localhost:5432/mydb?connection_limit=10&pool_timeout=20"

// Configuration du pool:
// connection_limit - Nombre max de connexions
// pool_timeout - Timeout en secondes
```

### 7. Logging et debugging

```typescript
// Activer les logs
const prisma = new PrismaClient({
  log: [
    { level: "query", emit: "event" },
    { level: "error", emit: "stdout" },
    { level: "warn", emit: "stdout" },
  ],
});

// Écouter les queries
prisma.$on("query", (e) => {
  console.log("Query: " + e.query);
  console.log("Duration: " + e.duration + "ms");
});

// En développement
const prisma = new PrismaClient({
  log: ["query", "info", "warn", "error"],
});
```

### 8. Middleware

```typescript
// Soft delete automatique
prisma.$use(async (params, next) => {
  if (params.model === "User") {
    if (params.action === "delete") {
      // Convertir delete en update
      params.action = "update";
      params.args.data = { deletedAt: new Date() };
    }

    if (params.action === "findMany" || params.action === "findFirst") {
      // Exclure les soft deleted
      params.args.where = {
        ...params.args.where,
        deletedAt: null,
      };
    }
  }

  return next(params);
});

// Logging automatique
prisma.$use(async (params, next) => {
  const before = Date.now();
  const result = await next(params);
  const after = Date.now();

  console.log(
    `Query ${params.model}.${params.action} took ${after - before}ms`,
  );

  return result;
});
```

### 9. Raw queries (quand nécessaire)

```typescript
// Pour des queries SQL complexes
const users = await prisma.$queryRaw`
  SELECT u.*, COUNT(p.id) as post_count
  FROM users u
  LEFT JOIN posts p ON p.author_id = u.id
  WHERE u.is_active = true
  GROUP BY u.id
  HAVING COUNT(p.id) > 10
`;

// Avec paramètres (protection injection SQL)
const email = "user@example.com";
const user = await prisma.$queryRaw`
  SELECT * FROM users WHERE email = ${email}
`;

// Execute (INSERT, UPDATE, DELETE)
await prisma.$executeRaw`
  UPDATE users SET last_login = NOW() WHERE id = ${userId}
`;
```

### 10. Type safety

```typescript
// Générer les types
import { Prisma } from "@prisma/client";

// Type d'un modèle
type User = Prisma.UserGetPayload<{}>;

// Type avec relations
type UserWithPosts = Prisma.UserGetPayload<{
  include: { posts: true };
}>;

// Type avec select
type UserBasic = Prisma.UserGetPayload<{
  select: { id: true; email: true };
}>;

// Input types
type UserCreateInput = Prisma.UserCreateInput;
type UserUpdateInput = Prisma.UserUpdateInput;

// Fonction avec type-safety
async function getUserWithPosts(id: number): Promise<UserWithPosts> {
  return await prisma.user.findUniqueOrThrow({
    where: { id },
    include: { posts: true },
  }),
  },
});

// Supprimer tous (attention!)
const result = await prisma.user.deleteMany();

// Soft delete (approche manuelle)
const user = await prisma.user.update({
  where: { id: 1 },
  data: {
    deletedAt: new Date(),
  },
});

// Requête excluant les soft deleted
const users = await prisma.user.findMany({
  where: {
    deletedAt: null,

});

// Relations imbriquées
const user = await prisma.user.findUnique({
  where: { id: 1 },
  include: {
    posts: {
      include: {
        comments: {
          include: {
            author: true,
          },
        },
      },
    },
  },
});

// Filtrer par relation
const users = await prisma.user.findMany({
  where: {
    posts: {
      some: {  // Au moins un post
        published: true,
      },
    },
  },
});

// Options: some, every, none
const users = await prisma.user.findMany({
  where: {
    posts: {
      every: { published: true },  // Tous publiés
    },
  },
});
```

### Select (projection)

```typescript
// Sélectionner des champs spécifiques
const users = await prisma.user.findMany({
  select: {
    id: true,
    email: true,
    firstName: true,
  },
});

// Select avec relations
const users = await prisma.user.findMany({
  select: {
    id: true,
    email: true,
    posts: {
      select: {
        id: true,
        title: true,
      },
    },
  },
});

// ⚠️ Ne peut pas combiner select et include
```

### Tri et pagination

```typescript
// Tri simple
const users = await prisma.user.findMany({
  orderBy: { createdAt: "desc" },
});

// Tri multiple
const users = await prisma.user.findMany({
  orderBy: [{ lastName: "asc" }, { firstName: "asc" }],
});

// Tri par relation
const users = await prisma.user.findMany({
  orderBy: {
    posts: {
      _count: "desc", // Tri par nombre de posts
    },
  },
});

// Pagination offset
const users = await prisma.user.findMany({
  skip: 20,
  take: 10, // Page 3 (10 par page)
});

// Cursor pagination (recommandé)
const users = await prisma.user.findMany({
  take: 10,
  skip: 1, // Sauter le curseur
  cursor: {
    id: lastUserId,
  },
  orderBy: { id: "asc" },
});
```

### Agrégation

````typescript
// Compter
const count = await prisma.user.count();
const activeCount = await prisma.user.count({
  where: { isActive: true },
});

// Agrégations
const result = await prisma.user.aggregate({
  _avg: { age: true },
  _count: { id: true },
  _max: { age: true },
  _min: { age: true },
  _sum: { age: true },
});

// Group by
const result = await prisma.user.groupBy({
  by: ["role"],
  _count: {
    id: true,
  },
  _avg: {
    age: true,
  },
  where: {
    isActive: true,
  },
  having: {
    age: {
      _avg: { gt: 18 },
    }

### Mettre à jour

```typescript
// TypeORM
await userRepository.update({ id: 1 }, { firstName: "Jane" });

// Ou
const user = await userRepository.findOne({ where: { id: 1 } });
user.firstName = "Jane";
await userRepository.save(user);

// Prisma
const user = await prisma.user.update({
  where: { id: 1 },
  data: { firstName: "Jane" },
});
````

### Supprimer

```typescript
// TypeORM
await userRepository.delete({ id: 1 });

// Soft delete
await userRepository.softDelete({ id: 1 });

// Prisma
await prisma.user.delete({ where: { id: 1 } });

// Soft delete (si configuré)
await prisma.user.update({
  where: { id: 1 },
  data: { deletedAt: new Date() },
});
```

---

## 🔒 Transactions

### TypeORM

```typescript
await dataSource.transaction(async (manager) => {
  const user = await manager.save(User, { email: "john@example.com" });
  await manager.save(Profile, { userId: user.id, bio: "Hello" });
});
```

### Prisma

```typescript
await prisma.$transaction(async (tx) => {
  const user = await tx.user.create({ data: { email: "john@example.com" } });
  await tx.profile.create({ data: { userId: user.id, bio: "Hello" } });
});
```

### Sequelize

```typescript
await sequelize.transaction(async (t) => {
  const user = await User.create(
    { email: "john@example.com" },
    { transaction: t },
  );
  await Profile.create({ userId: user.id, bio: "Hello" }, { transaction: t });
});
```

---

## 🚀 Optimisations et Bonnes Pratiques

### 1. N+1 Problem

```typescript
// ❌ Mauvais - N+1 queries
const users = await prisma.user.findMany();
for (const user of users) {
  user.posts = await prisma.post.findMany({ where: { userId: user.id } });
}

// ✅ Bon - Une seule query avec include
const users = await prisma.user.findMany({
  include: { posts: true },
});
```

### 2. Select minimal

```typescript
// ❌ Éviter - récupère toutes les colonnes
const users = await prisma.user.findMany();

// ✅ Bon - seulement les colonnes nécessaires
const users = await prisma.user.findMany({
  select: { id: true, email: true, firstName: true },
});
```

### 3. Pagination

```typescript
// ✅ Bon - cursor-based pagination
const users = await prisma.user.findMany({
  take: 10,
  skip: 1,
  cursor: { id: lastId },
});

// Ou offset pagination
const users = await prisma.user.findMany({
  take: 10,
  skip: page * 10,
});
```

### 4. Index

```prisma
// Prisma
model User {
  email String @unique

  @@index([firstName, lastName])
  @@index([createdAt])
}
```

### 5. Validation

```typescript
// TypeORM avec class-validator
import { IsEmail, Length, IsBoolean } from "class-validator";

@Entity()
export class User {
  @Column()
  @IsEmail()
  email: string;

  @Column()
  @Length(2, 100)
  firstName: string;

  @Column()
  @IsBoolean()
  isActive: boolean;
}
```

---

## 📚 Ressources

- [TypeORM Documentation](https://typeorm.io/)
- [Prisma Documentation](https://www.prisma.io/docs)
- [Sequelize Documentation](https://sequelize.org/)
- [Mongoose (MongoDB)](https://mongoosejs.com/)
