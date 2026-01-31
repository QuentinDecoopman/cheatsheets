
# Conventions & Bonnes Pratiques Drizzle ORM

## 📋 Introduction à Drizzle

Drizzle est un ORM TypeScript léger et performant avec une approche "SQL-like" qui permet d'écrire des requêtes type-safe proches du SQL natif.

**Avantages de Drizzle:**

- Type-safety complète avec TypeScript
- Syntaxe proche du SQL (courbe d'apprentissage réduite)
- Léger et performant (pas de runtime, juste du code généré)
- Serverless-ready (compatible edge functions)
- Support PostgreSQL, MySQL, SQLite
- Migrations automatiques avec Drizzle Kit
- Drizzle Studio pour visualiser les données

**Composants:**

- **Drizzle ORM** : Core pour les requêtes
- **Drizzle Kit** : CLI pour migrations et introspection
- **Drizzle Studio** : Interface graphique

---

## 🎯 Conventions de Nommage

### Tables

```ts
// ✅ Bon - snake_case, pluriel
export const users = pgTable("users", { ... });
export const order_items = pgTable("order_items", { ... });

// ❌ Éviter
export const User = pgTable("User", { ... });
export const OrderItem = pgTable("orderItem", { ... });
```

### Colonnes

```ts
// ✅ Bon - snake_case dans la BDD, camelCase pour la variable
export const users = pgTable("users", {
  id: serial("id").primaryKey(),
  firstName: text("first_name"),      // Variable camelCase, colonne snake_case
  lastName: text("last_name"),
  createdAt: timestamp("created_at").defaultNow(),
});

// ❌ Éviter
export const users = pgTable("users", {
  FirstName: text("FirstName"),       // Incohérent
  created_at: timestamp("createdAt"), // Inversé
});
```

### Relations

```ts
// ✅ Bon - nommage clair et cohérent
export const usersRelations = relations(users, ({ many }) => ({
  posts: many(posts),           // Pluriel pour one-to-many
  profile: one(profiles),       // Singulier pour one-to-one
}));

export const postsRelations = relations(posts, ({ one }) => ({
  author: one(users, {          // Nom sémantique
    fields: [posts.authorId],
    references: [users.id],
  }),
}));
```

---

## 🏗️ Structure du Projet

### Organisation recommandée

```
src/
├── db/
│   ├── index.ts          # Connexion et export de db
│   ├── schema/
│   │   ├── index.ts      # Export de tous les schémas
│   │   ├── users.ts      # Table users + relations
│   │   ├── posts.ts      # Table posts + relations
│   │   └── ...
│   └── migrations/       # (généré par drizzle-kit)
├── drizzle.config.ts     # Configuration Drizzle Kit
└── ...
```

### Fichier de connexion

```ts
// src/db/index.ts
import { drizzle } from "drizzle-orm/postgres-js";
import postgres from "postgres";
import * as schema from "./schema";

const connectionString = process.env.DATABASE_URL!;

// Configuration pour différents environnements
const client = postgres(connectionString, {
  max: process.env.NODE_ENV === "production" ? 10 : 1,
  idle_timeout: 20,
  connect_timeout: 10,
});

export const db = drizzle(client, { schema });
```

### Export des schémas

```ts
// src/db/schema/index.ts
export * from "./users";
export * from "./posts";
export * from "./comments";
```

---

## 📝 Définition des Schémas

### Types de colonnes courants (PostgreSQL)

```ts
import {
  pgTable,
  serial,
  integer,
  bigint,
  text,
  varchar,
  boolean,
  timestamp,
  date,
  json,
  jsonb,
  uuid,
  decimal,
  doublePrecision,
  pgEnum,
} from "drizzle-orm/pg-core";

// Enum
export const roleEnum = pgEnum("role", ["user", "admin", "moderator"]);

export const users = pgTable("users", {
  // Identifiants
  id: serial("id").primaryKey(),
  uuid: uuid("uuid").defaultRandom(),

  // Texte
  name: text("name").notNull(),
  email: varchar("email", { length: 255 }).notNull().unique(),
  bio: text("bio"),

  // Nombres
  age: integer("age"),
  balance: decimal("balance", { precision: 10, scale: 2 }),

  // Boolean
  isActive: boolean("is_active").default(true),

  // Enum
  role: roleEnum("role").default("user"),

  // JSON
  metadata: jsonb("metadata"),

  // Dates
  createdAt: timestamp("created_at").defaultNow(),
  updatedAt: timestamp("updated_at").defaultNow().$onUpdate(() => new Date()),
  birthDate: date("birth_date"),
});
```

### Contraintes et index

```ts
import { pgTable, serial, text, varchar, index, uniqueIndex } from "drizzle-orm/pg-core";

export const users = pgTable("users", {
  id: serial("id").primaryKey(),
  email: varchar("email", { length: 255 }).notNull().unique(),
  firstName: text("first_name").notNull(),
  lastName: text("last_name").notNull(),
  country: text("country"),
}, (table) => ({
  // Index simple
  countryIdx: index("country_idx").on(table.country),

  // Index composé
  nameIdx: index("name_idx").on(table.firstName, table.lastName),

  // Index unique
  emailIdx: uniqueIndex("email_idx").on(table.email),
}));
```

---

## 🔗 Relations

### One-to-One

```ts
export const users = pgTable("users", {
  id: serial("id").primaryKey(),
  name: text("name").notNull(),
});

export const profiles = pgTable("profiles", {
  id: serial("id").primaryKey(),
  bio: text("bio"),
  userId: integer("user_id").references(() => users.id).unique(),
});

export const usersRelations = relations(users, ({ one }) => ({
  profile: one(profiles, {
    fields: [users.id],
    references: [profiles.userId],
  }),
}));

export const profilesRelations = relations(profiles, ({ one }) => ({
  user: one(users, {
    fields: [profiles.userId],
    references: [users.id],
  }),
}));
```

### One-to-Many

```ts
export const users = pgTable("users", {
  id: serial("id").primaryKey(),
  name: text("name").notNull(),
});

export const posts = pgTable("posts", {
  id: serial("id").primaryKey(),
  title: text("title").notNull(),
  authorId: integer("author_id").references(() => users.id),
});

export const usersRelations = relations(users, ({ many }) => ({
  posts: many(posts),
}));

export const postsRelations = relations(posts, ({ one }) => ({
  author: one(users, {
    fields: [posts.authorId],
    references: [users.id],
  }),
}));
```

### Many-to-Many

```ts
export const users = pgTable("users", {
  id: serial("id").primaryKey(),
  name: text("name").notNull(),
});

export const groups = pgTable("groups", {
  id: serial("id").primaryKey(),
  name: text("name").notNull(),
});

// Table de jonction
export const usersToGroups = pgTable("users_to_groups", {
  userId: integer("user_id").references(() => users.id).notNull(),
  groupId: integer("group_id").references(() => groups.id).notNull(),
}, (t) => ({
  pk: primaryKey({ columns: [t.userId, t.groupId] }),
}));

export const usersRelations = relations(users, ({ many }) => ({
  usersToGroups: many(usersToGroups),
}));

export const groupsRelations = relations(groups, ({ many }) => ({
  usersToGroups: many(usersToGroups),
}));

export const usersToGroupsRelations = relations(usersToGroups, ({ one }) => ({
  user: one(users, {
    fields: [usersToGroups.userId],
    references: [users.id],
  }),
  group: one(groups, {
    fields: [usersToGroups.groupId],
    references: [groups.id],
  }),
}));
```

---

## 🔍 Requêtes

### Select API (SQL-like)

```ts
import { eq, and, or, like, gt, lt, gte, lte, ne, isNull, isNotNull, inArray, between, desc, asc, sql } from "drizzle-orm";

// Sélection simple
const allUsers = await db.select().from(users);

// Avec conditions
const activeUsers = await db.select()
  .from(users)
  .where(eq(users.isActive, true));

// Conditions multiples
const filteredUsers = await db.select()
  .from(users)
  .where(and(
    eq(users.role, "admin"),
    gt(users.createdAt, new Date("2024-01-01")),
    like(users.email, "%@company.com")
  ));

// OR conditions
const specialUsers = await db.select()
  .from(users)
  .where(or(
    eq(users.role, "admin"),
    eq(users.role, "moderator")
  ));

// Tri, limite, offset
const paginatedUsers = await db.select()
  .from(users)
  .orderBy(desc(users.createdAt))
  .limit(10)
  .offset(20);

// Sélection partielle
const userEmails = await db.select({
  id: users.id,
  email: users.email,
}).from(users);
```

### Query API (avec relations)

```ts
// Tous les utilisateurs avec leurs posts
const usersWithPosts = await db.query.users.findMany({
  with: {
    posts: true,
  },
});

// Avec conditions et tri
const activeUsersWithPosts = await db.query.users.findMany({
  where: eq(users.isActive, true),
  with: {
    posts: {
      where: eq(posts.published, true),
      orderBy: desc(posts.createdAt),
      limit: 5,
    },
  },
  orderBy: desc(users.createdAt),
  limit: 10,
});

// Un seul résultat
const user = await db.query.users.findFirst({
  where: eq(users.id, 1),
  with: {
    profile: true,
    posts: true,
  },
});
```

### Jointures

```ts
// Inner join
const result = await db.select({
  userName: users.name,
  postTitle: posts.title,
})
  .from(users)
  .innerJoin(posts, eq(users.id, posts.authorId));

// Left join
const usersWithPosts = await db.select()
  .from(users)
  .leftJoin(posts, eq(users.id, posts.authorId));
```

### Agrégations

```ts
import { count, sum, avg, min, max } from "drizzle-orm";

// Count
const userCount = await db.select({ count: count() }).from(users);

// Group by avec agrégation
const postsByUser = await db.select({
  authorId: posts.authorId,
  postCount: count(),
})
  .from(posts)
  .groupBy(posts.authorId);
```

---

## ✏️ Mutations

### Insert

```ts
// Insert simple
const newUser = await db.insert(users)
  .values({
    name: "Alice",
    email: "alice@example.com",
  })
  .returning();

// Insert multiple
const newUsers = await db.insert(users)
  .values([
    { name: "Bob", email: "bob@example.com" },
    { name: "Charlie", email: "charlie@example.com" },
  ])
  .returning();

// On conflict (upsert)
await db.insert(users)
  .values({ id: 1, name: "Alice", email: "alice@example.com" })
  .onConflictDoUpdate({
    target: users.email,
    set: { name: "Alice Updated" },
  });

// On conflict do nothing
await db.insert(users)
  .values({ name: "Alice", email: "alice@example.com" })
  .onConflictDoNothing();
```

### Update

```ts
// Update avec condition
await db.update(users)
  .set({ name: "Alice Updated", updatedAt: new Date() })
  .where(eq(users.id, 1));

// Update avec returning
const updated = await db.update(users)
  .set({ isActive: false })
  .where(eq(users.role, "inactive"))
  .returning();
```

### Delete

```ts
// Delete avec condition
await db.delete(users).where(eq(users.id, 1));

// Delete avec returning
const deleted = await db.delete(users)
  .where(eq(users.isActive, false))
  .returning();
```

---

## 🔄 Transactions

```ts
// Transaction simple
await db.transaction(async (tx) => {
  const user = await tx.insert(users)
    .values({ name: "Alice", email: "alice@example.com" })
    .returning();

  await tx.insert(posts)
    .values({ title: "Premier post", authorId: user[0].id });
});

// Transaction avec rollback manuel
await db.transaction(async (tx) => {
  try {
    await tx.insert(users).values({ name: "Bob", email: "bob@example.com" });

    // Condition de rollback
    const count = await tx.select({ count: count() }).from(users);
    if (count[0].count > 1000) {
      tx.rollback();
    }
  } catch (error) {
    // Rollback automatique en cas d'erreur
    throw error;
  }
});
```

---

## 🛡️ Validation avec Zod

```ts
import { createInsertSchema, createSelectSchema } from "drizzle-zod";
import { z } from "zod";

// Générer les schémas Zod depuis le schéma Drizzle
export const insertUserSchema = createInsertSchema(users, {
  email: z.string().email("Email invalide"),
  name: z.string().min(2, "Nom trop court"),
});

export const selectUserSchema = createSelectSchema(users);

// Utilisation
const validatedData = insertUserSchema.parse({
  name: "Alice",
  email: "alice@example.com",
});

await db.insert(users).values(validatedData);
```

---

## 🚀 Bonnes Pratiques

### 1. Toujours utiliser les types générés

```ts
// ✅ Bon - Types inférés
import { users } from "./schema";
import { InferSelectModel, InferInsertModel } from "drizzle-orm";

type User = InferSelectModel<typeof users>;
type NewUser = InferInsertModel<typeof users>;

// Utilisation
async function createUser(data: NewUser): Promise<User> {
  const result = await db.insert(users).values(data).returning();
  return result[0];
}
```

### 2. Centraliser les requêtes complexes

```ts
// src/db/queries/users.ts
export const userQueries = {
  findById: (id: number) =>
    db.query.users.findFirst({
      where: eq(users.id, id),
      with: { profile: true },
    }),

  findByEmail: (email: string) =>
    db.query.users.findFirst({
      where: eq(users.email, email),
    }),

  findActive: () =>
    db.query.users.findMany({
      where: eq(users.isActive, true),
      orderBy: desc(users.createdAt),
    }),
};
```

### 3. Utiliser les prepared statements pour les performances

```ts
import { placeholder } from "drizzle-orm";

const prepared = db.select()
  .from(users)
  .where(eq(users.id, placeholder("id")))
  .prepare("getUserById");

// Utilisation (plus rapide pour les requêtes répétées)
const user = await prepared.execute({ id: 1 });
```

### 4. Gestion des erreurs

```ts
import { DrizzleError } from "drizzle-orm";

try {
  await db.insert(users).values({ email: "duplicate@example.com" });
} catch (error) {
  if (error instanceof DrizzleError) {
    // Gérer l'erreur Drizzle
    console.error("Erreur Drizzle:", error.message);
  }
  throw error;
}
```

