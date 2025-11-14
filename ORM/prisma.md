# Prisma

## Documentation

https://www.prisma.io/docs/

## Installation (Node.js)

```sh
npm install prisma --save-dev
npx prisma init
```

L'initialisation crée un dossier `prisma/` contenant `schema.prisma` et met à jour `.env`.

## Exemple `schema.prisma` (PostgreSQL)

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id    Int    @id @default(autoincrement())
  email String @unique
  name  String?
}
```

## Commandes Prisma utiles

- Générer le client : `npx prisma generate`
- Créer une migration : `npx prisma migrate dev --name init`
- Appliquer les migrations en production : `npx prisma migrate deploy`
- Ouvrir GUI : `npx prisma studio`

## Exemples (Node.js)

```js
import { PrismaClient } from "@prisma/client";
const prisma = new PrismaClient();

async function main() {
  const user = await prisma.user.create({
    data: { email: "alice@example.com", name: "Alice" },
  });
  console.log(user);
}

main()
  .catch((e) => console.error(e))
  .finally(async () => await prisma.$disconnect());
```

## Lien avec PostgreSQL

Dans `.env` :

```
DATABASE_URL="postgresql://USER:PASSWORD@HOST:PORT/DATABASE"
```

Prisma gère le schéma via `schema.prisma` ; les migrations transforment ce schéma en objets SQL appliqués à la base.

## Ressources

- Docs : https://www.prisma.io/docs/
- Examples : https://www.prisma.io/examples

## Commandes courantes

Commandes Prisma fréquemment utilisées :

```sh
# initialiser un projet prisma (crée prisma/schema.prisma)
npx prisma init

# synchroniser le schéma avec la DB sans créer de migration (utile en dev rapide)
npx prisma db push

# récupérer le schéma existant depuis la DB
npx prisma db pull

# créer une migration et appliquer en local
npx prisma migrate dev --name "nom_migration"

# appliquer les migrations en production
npx prisma migrate deploy

# générer le client (après modification du schema)
npx prisma generate

# ouvrir Prisma Studio (GUI)
npx prisma studio

# formater le schema
npx prisma format

# afficher l'état des migrations
npx prisma migrate status
```

Exemple de scripts `package.json` utiles :

```json
{
  "scripts": {
    "prisma:generate": "prisma generate",
    "prisma:migrate": "prisma migrate dev",
    "prisma:studio": "prisma studio"
  }
}
```

