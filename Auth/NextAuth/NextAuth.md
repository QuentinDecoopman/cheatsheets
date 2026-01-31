# NextAuth.js (Auth.js)

NextAuth.js (maintenant Auth.js v5) est la solution d'authentification la plus populaire pour Next.js. Elle supporte de nombreux providers et est très flexible.

---

## Installation

```bash
npm install next-auth
# ou
pnpm add next-auth
# ou
yarn add next-auth
```

---

## Configuration de base (App Router)

### 1. Créer le fichier de configuration

```ts
// auth.ts (à la racine du projet)
import NextAuth from "next-auth";
import Google from "next-auth/providers/google";
import GitHub from "next-auth/providers/github";
import Credentials from "next-auth/providers/credentials";

export const { handlers, signIn, signOut, auth } = NextAuth({
  providers: [
    Google({
      clientId: process.env.GOOGLE_CLIENT_ID,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET,
    }),
    GitHub({
      clientId: process.env.GITHUB_CLIENT_ID,
      clientSecret: process.env.GITHUB_CLIENT_SECRET,
    }),
    Credentials({
      credentials: {
        email: { label: "Email", type: "email" },
        password: { label: "Password", type: "password" },
      },
      async authorize(credentials) {
        // Logique de validation
        const user = await validateUser(
          credentials.email,
          credentials.password,
        );
        return user ?? null;
      },
    }),
  ],
});
```

### 2. Créer le handler API

```ts
// app/api/auth/[...nextauth]/route.ts
import { handlers } from "@/auth";

export const { GET, POST } = handlers;
```

### 3. Créer le middleware

```ts
// middleware.ts
export { auth as middleware } from "@/auth";

export const config = {
  matcher: ["/dashboard/:path*", "/profile/:path*"],
};
```

---

## Utilisation

### Récupérer la session côté serveur (RSC)

```tsx
// app/page.tsx
import { auth } from "@/auth";

export default async function Page() {
  const session = await auth();

  if (!session) {
    return <div>Non connecté</div>;
  }

  return <div>Bonjour {session.user?.name}</div>;
}
```

### Récupérer la session côté client

```tsx
"use client";

import { useSession } from "next-auth/react";

export function UserProfile() {
  const { data: session, status } = useSession();

  if (status === "loading") return <div>Chargement...</div>;
  if (status === "unauthenticated") return <div>Non connecté</div>;

  return <div>Bonjour {session?.user?.name}</div>;
}
```

### Provider de session (obligatoire pour le client)

```tsx
// app/providers.tsx
"use client";

import { SessionProvider } from "next-auth/react";

export function Providers({ children }: { children: React.ReactNode }) {
  return <SessionProvider>{children}</SessionProvider>;
}
```

```tsx
// app/layout.tsx
import { Providers } from "./providers";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html>
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  );
}
```

---

## Connexion / Déconnexion

### Avec Server Actions ✅

```tsx
// app/login/page.tsx
import { signIn, signOut, auth } from "@/auth";

export default async function LoginPage() {
  const session = await auth();

  return (
    <div>
      {session ? (
        <form
          action={async () => {
            "use server";
            await signOut();
          }}
        >
          <button type="submit">Se déconnecter</button>
        </form>
      ) : (
        <>
          {/* Connexion OAuth */}
          <form
            action={async () => {
              "use server";
              await signIn("google");
            }}
          >
            <button type="submit">Se connecter avec Google</button>
          </form>

          <form
            action={async () => {
              "use server";
              await signIn("github");
            }}
          >
            <button type="submit">Se connecter avec GitHub</button>
          </form>

          {/* Connexion Credentials */}
          <form
            action={async (formData) => {
              "use server";
              await signIn("credentials", formData);
            }}
          >
            <input name="email" type="email" placeholder="Email" />
            <input name="password" type="password" placeholder="Mot de passe" />
            <button type="submit">Se connecter</button>
          </form>
        </>
      )}
    </div>
  );
}
```

### Côté client

```tsx
"use client";

import { signIn, signOut } from "next-auth/react";

export function AuthButtons() {
  return (
    <div>
      <button onClick={() => signIn("google")}>Connexion Google</button>
      <button onClick={() => signIn("github")}>Connexion GitHub</button>
      <button onClick={() => signOut()}>Déconnexion</button>
    </div>
  );
}
```

---

## Callbacks

Les callbacks permettent de personnaliser le comportement de l'authentification.

```ts
// auth.ts
export const { handlers, signIn, signOut, auth } = NextAuth({
  providers: [...],
  callbacks: {
    // Appelé lors de la création/mise à jour du JWT
    async jwt({ token, user, account, profile }) {
      if (user) {
        token.id = user.id;
        token.role = user.role; // Ajouter des données custom
      }
      return token;
    },

    // Appelé lors de la création de la session
    async session({ session, token }) {
      if (token) {
        session.user.id = token.id as string;
        session.user.role = token.role as string;
      }
      return session;
    },

    // Contrôler l'accès à certaines pages
    async authorized({ auth, request: { nextUrl } }) {
      const isLoggedIn = !!auth?.user;
      const isOnDashboard = nextUrl.pathname.startsWith("/dashboard");

      if (isOnDashboard) {
        if (isLoggedIn) return true;
        return false; // Redirect to login
      }

      return true;
    },

    // Personnaliser la redirection après connexion
    async redirect({ url, baseUrl }) {
      if (url.startsWith("/")) return `${baseUrl}${url}`;
      if (new URL(url).origin === baseUrl) return url;
      return baseUrl;
    },
  },
});
```

---

## Adapter pour base de données

### Prisma Adapter

```bash
npm install @auth/prisma-adapter
```

```ts
// auth.ts
import NextAuth from "next-auth";
import { PrismaAdapter } from "@auth/prisma-adapter";
import { prisma } from "@/lib/prisma";

export const { handlers, signIn, signOut, auth } = NextAuth({
  adapter: PrismaAdapter(prisma),
  providers: [...],
  session: {
    strategy: "database", // ou "jwt"
  },
});
```

### Schéma Prisma requis

```prisma
model User {
  id            String    @id @default(cuid())
  name          String?
  email         String    @unique
  emailVerified DateTime?
  image         String?
  accounts      Account[]
  sessions      Session[]
}

model Account {
  id                String  @id @default(cuid())
  userId            String
  type              String
  provider          String
  providerAccountId String
  refresh_token     String? @db.Text
  access_token      String? @db.Text
  expires_at        Int?
  token_type        String?
  scope             String?
  id_token          String? @db.Text
  session_state     String?

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([provider, providerAccountId])
}

model Session {
  id           String   @id @default(cuid())
  sessionToken String   @unique
  userId       String
  expires      DateTime
  user         User     @relation(fields: [userId], references: [id], onDelete: Cascade)
}

model VerificationToken {
  identifier String
  token      String   @unique
  expires    DateTime

  @@unique([identifier, token])
}
```

---

## Types personnalisés

```ts
// types/next-auth.d.ts
import { DefaultSession, DefaultUser } from "next-auth";
import { JWT, DefaultJWT } from "next-auth/jwt";

declare module "next-auth" {
  interface Session {
    user: {
      id: string;
      role: string;
    } & DefaultSession["user"];
  }

  interface User extends DefaultUser {
    role: string;
  }
}

declare module "next-auth/jwt" {
  interface JWT extends DefaultJWT {
    id: string;
    role: string;
  }
}
```

---

## Protection des routes

### Avec le middleware

```ts
// middleware.ts
import { auth } from "@/auth";
import { NextResponse } from "next/server";

export default auth((req) => {
  const isLoggedIn = !!req.auth;
  const isOnDashboard = req.nextUrl.pathname.startsWith("/dashboard");
  const isOnAdmin = req.nextUrl.pathname.startsWith("/admin");

  // Protection dashboard
  if (isOnDashboard && !isLoggedIn) {
    return NextResponse.redirect(new URL("/login", req.url));
  }

  // Protection admin avec vérification du rôle
  if (isOnAdmin) {
    if (!isLoggedIn) {
      return NextResponse.redirect(new URL("/login", req.url));
    }
    if (req.auth?.user?.role !== "admin") {
      return NextResponse.redirect(new URL("/unauthorized", req.url));
    }
  }

  return NextResponse.next();
});

export const config = {
  matcher: ["/dashboard/:path*", "/admin/:path*"],
};
```

### Dans un composant serveur

```tsx
import { auth } from "@/auth";
import { redirect } from "next/navigation";

export default async function DashboardPage() {
  const session = await auth();

  if (!session) {
    redirect("/login");
  }

  if (session.user.role !== "admin") {
    redirect("/unauthorized");
  }

  return <div>Dashboard Admin</div>;
}
```

---

## Providers disponibles

NextAuth supporte de nombreux providers OAuth :

| Provider    | Import                            |
| ----------- | --------------------------------- |
| Google      | `next-auth/providers/google`      |
| GitHub      | `next-auth/providers/github`      |
| Discord     | `next-auth/providers/discord`     |
| Twitter     | `next-auth/providers/twitter`     |
| Facebook    | `next-auth/providers/facebook`    |
| Apple       | `next-auth/providers/apple`       |
| LinkedIn    | `next-auth/providers/linkedin`    |
| Spotify     | `next-auth/providers/spotify`     |
| Credentials | `next-auth/providers/credentials` |
| Email       | `next-auth/providers/email`       |

---

## Particularités et bonnes pratiques

### ✅ Ce qui fonctionne bien

- **Server Actions** : NextAuth v5 supporte parfaitement les Server Actions
- **Middleware** : Protection des routes efficace au niveau edge
- **RSC** : Récupération de session dans les React Server Components

### ⚠️ Points d'attention

```tsx
// ⚠️ Ne pas oublier le SessionProvider pour le client
// Sans lui, useSession() ne fonctionnera pas
<SessionProvider>{children}</SessionProvider>
```

```tsx
// ⚠️ Différence entre import serveur et client
// Serveur (RSC, Server Actions)
import { auth, signIn, signOut } from "@/auth";

// Client
import { useSession, signIn, signOut } from "next-auth/react";
```

```tsx
// ⚠️ Le provider Credentials ne crée pas de session en DB par défaut
// Utiliser strategy: "jwt" ou implémenter la logique custom
```
