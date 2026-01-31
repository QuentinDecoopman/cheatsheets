
# Drizzle ORM

## Documentation

https://orm.drizzle.team/docs/overview

## Installation (Node.js)

```sh
# Installation de Drizzle ORM
npm install drizzle-orm

# Installation du driver selon la base de données
npm install postgres         # PostgreSQL (recommandé)
npm install @neondatabase/serverless  # Neon (serverless)
npm install mysql2           # MySQL
npm install better-sqlite3   # SQLite

# Installation de Drizzle Kit (migrations et introspection)
npm install drizzle-kit --save-dev
```

## Configuration `drizzle.config.ts`

```ts
import { defineConfig } from "drizzle-kit";

export default defineConfig({
  schema: "./src/db/schema.ts",
  out: "./drizzle",
  dialect: "postgresql",
  dbCredentials: {
    url: process.env.DATABASE_URL!,
  },
});
```

## Exemple de schéma (PostgreSQL)

```ts
// src/db/schema.ts
import { pgTable, serial, text, varchar, timestamp } from "drizzle-orm/pg-core";

export const users = pgTable("users", {
  id: serial("id").primaryKey(),
  email: varchar("email", { length: 255 }).notNull().unique(),
  name: text("name"),
  createdAt: timestamp("created_at").defaultNow(),
});
```

## Connexion à la base de données

```ts
// src/db/index.ts
import { drizzle } from "drizzle-orm/postgres-js";
import postgres from "postgres";
import * as schema from "./schema";

const connectionString = process.env.DATABASE_URL!;
const client = postgres(connectionString);

export const db = drizzle(client, { schema });
```

## Commandes Drizzle Kit utiles

```sh
# Générer les migrations
npx drizzle-kit generate

# Appliquer les migrations
npx drizzle-kit migrate

# Pousser le schéma directement (sans migration)
npx drizzle-kit push

# Introspection (générer le schéma depuis la BDD)
npx drizzle-kit introspect

# Ouvrir Drizzle Studio (GUI)
npx drizzle-kit studio
```

## Exemples de requêtes (Node.js)

### CRUD basique

```ts
import { db } from "./db";
import { users } from "./db/schema";
import { eq } from "drizzle-orm";

// CREATE
const newUser = await db.insert(users).values({
  email: "alice@example.com",
  name: "Alice",
}).returning();

// READ - tous les utilisateurs
const allUsers = await db.select().from(users);

// READ - avec condition
const user = await db.select().from(users).where(eq(users.email, "alice@example.com"));

// UPDATE
await db.update(users)
  .set({ name: "Alice Updated" })
  .where(eq(users.id, 1));

// DELETE
await db.delete(users).where(eq(users.id, 1));
```

### Requêtes avancées

```ts
import { eq, and, or, like, gt, lt, desc, asc } from "drizzle-orm";

// Conditions multiples
const filteredUsers = await db.select()
  .from(users)
  .where(and(
    like(users.email, "%@gmail.com"),
    gt(users.createdAt, new Date("2024-01-01"))
  ));

// Tri et limite
const recentUsers = await db.select()
  .from(users)
  .orderBy(desc(users.createdAt))
  .limit(10);

// Sélection de colonnes spécifiques
const emails = await db.select({
  email: users.email,
  name: users.name,
}).from(users);
```

## Relations

### Définition des relations

```ts
// schema.ts
import { pgTable, serial, text, integer, timestamp } from "drizzle-orm/pg-core";
import { relations } from "drizzle-orm";

export const users = pgTable("users", {
  id: serial("id").primaryKey(),
  name: text("name").notNull(),
});

export const posts = pgTable("posts", {
  id: serial("id").primaryKey(),
  title: text("title").notNull(),
  content: text("content"),
  authorId: integer("author_id").references(() => users.id),
  createdAt: timestamp("created_at").defaultNow(),
});

// Définition des relations
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

### Requêtes avec relations

```ts
// Requête avec relations (query API)
const usersWithPosts = await db.query.users.findMany({
  with: {
    posts: true,
  },
});

// Requête avec conditions sur les relations
const usersWithRecentPosts = await db.query.users.findMany({
  with: {
    posts: {
      where: gt(posts.createdAt, new Date("2024-01-01")),
      orderBy: desc(posts.createdAt),
    },
  },
});
```

## Lien avec PostgreSQL

Dans `.env` :

```
DATABASE_URL="postgresql://USER:PASSWORD@HOST:PORT/DATABASE"
```

## Transactions

```ts
await db.transaction(async (tx) => {
  const user = await tx.insert(users).values({ name: "Bob", email: "bob@example.com" }).returning();
  await tx.insert(posts).values({ title: "Premier post", authorId: user[0].id });
});
```

## Scripts `package.json` utiles

```json
{
  "scripts": {
    "db:generate": "drizzle-kit generate",
    "db:migrate": "drizzle-kit migrate",
    "db:push": "drizzle-kit push",
    "db:studio": "drizzle-kit studio",
    "db:introspect": "drizzle-kit introspect"
  }
}
```
