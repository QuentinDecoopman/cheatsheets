# Better Auth

Better Auth est une librairie d'authentification moderne pour Next.js, TypeScript-first, simple à configurer et extensible.

---

## Installation

```bash
npm install better-auth
# ou
pnpm add better-auth
# ou
yarn add better-auth
```

---

## Configuration de base

### 1. Créer le fichier de configuration

```ts
// lib/auth.ts
import { betterAuth } from "better-auth";
import { prismaAdapter } from "better-auth/adapters/prisma";
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();

export const auth = betterAuth({
  database: prismaAdapter(prisma, {
    provider: "postgresql", // ou "mysql", "sqlite"
  }),
  emailAndPassword: {
    enabled: true,
  },
});
```

### 2. Créer le handler API

```ts
// app/api/auth/[...all]/route.ts
import { auth } from "@/lib/auth";
import { toNextJsHandler } from "better-auth/next-js";

export const { GET, POST } = toNextJsHandler(auth);
```

### 3. Créer le client

```ts
// lib/auth-client.ts
import { createAuthClient } from "better-auth/react";

export const authClient = createAuthClient({
  baseURL: process.env.NEXT_PUBLIC_APP_URL,
});

export const { signIn, signUp, signOut, useSession } = authClient;
```

---

## ⚠️ Particularités importantes

### 🚨 Ne pas passer les requêtes dans les Server Actions

> **ATTENTION** : Better Auth ne fonctionne pas correctement si vous essayez de passer l'objet `request` ou d'utiliser des Server Actions pour gérer l'authentification.

### 🚨 Validation des formulaires

> **ATTENTION** : L'architecture de Better Auth (basée sur les API Routes côté client) empêche l'utilisation de librairies tierces de validation côté serveur comme **Zod avec Server Actions** ou **react-hook-form** avec validation serveur.

Better Auth gère la validation côté serveur **nativement**. Les erreurs de validation sont retournées directement par les méthodes du client.

### 🚨 Ne pas passer les requêtes dans les Server Actions

#### ❌ Ce qu'il ne faut PAS faire

```ts
// ❌ NE PAS FAIRE - Server Action avec requête
"use server";

import { auth } from "@/lib/auth";

export async function loginAction(formData: FormData) {
  // Ceci ne fonctionnera PAS correctement
  const session = await auth.api.getSession({
    headers: headers(),
  });
}
```

```ts
// ❌ NE PAS FAIRE - Passer la request dans une Server Action
"use server";

export async function getUser(request: Request) {
  // Better Auth perd le contexte des cookies
  const session = await auth.api.getSession({ headers: request.headers });
}
```

#### ✅ Ce qu'il faut faire

```ts
// ✅ CORRECT - Utiliser les API Routes
// app/api/auth/[...all]/route.ts
import { auth } from "@/lib/auth";
import { toNextJsHandler } from "better-auth/next-js";

export const { GET, POST } = toNextJsHandler(auth);
```

```ts
// ✅ CORRECT - Côté client, utiliser le client auth
"use client";

import { signIn, signUp, useSession } from "@/lib/auth-client";

export function LoginForm() {
  const { data: session } = useSession();

  const handleLogin = async () => {
    await signIn.email({
      email: "user@example.com",
      password: "password123",
    });
  };
}
```

```ts
// ✅ CORRECT - Pour récupérer la session côté serveur (RSC)
import { auth } from "@/lib/auth";
import { headers } from "next/headers";

export default async function Page() {
  const session = await auth.api.getSession({
    headers: await headers(),
  });
}
```

---

## Utilisation

### Inscription

```tsx
"use client";

import { signUp } from "@/lib/auth-client";

const handleSignUp = async () => {
  const { data, error } = await signUp.email({
    email: "user@example.com",
    password: "password123",
    name: "John Doe",
  });

  if (error) {
    console.error(error.message);
  }
};
```

### Connexion

```tsx
"use client";

import { signIn } from "@/lib/auth-client";

// Connexion email/password
const handleSignIn = async () => {
  const { data, error } = await signIn.email({
    email: "user@example.com",
    password: "password123",
  });
};

// Connexion OAuth (Google, GitHub, etc.)
const handleOAuthSignIn = async () => {
  await signIn.social({
    provider: "google",
    callbackURL: "/dashboard",
  });
};
```

### Déconnexion

```tsx
"use client";

import { signOut } from "@/lib/auth-client";

const handleSignOut = async () => {
  await signOut();
};
```

### Récupérer la session

```tsx
// Côté client
"use client";

import { useSession } from "@/lib/auth-client";

export function UserProfile() {
  const { data: session, isPending } = useSession();

  if (isPending) return <div>Chargement...</div>;
  if (!session) return <div>Non connecté</div>;

  return <div>Bonjour {session.user.name}</div>;
}
```

```tsx
// Côté serveur (RSC)
import { auth } from "@/lib/auth";
import { headers } from "next/headers";

export default async function Page() {
  const session = await auth.api.getSession({
    headers: await headers(),
  });

  if (!session) {
    redirect("/login");
  }

  return <div>Bonjour {session.user.name}</div>;
}
```

---

## Providers OAuth

### Configuration

```ts
// lib/auth.ts
import { betterAuth } from "better-auth";

export const auth = betterAuth({
  // ...
  socialProviders: {
    google: {
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
    },
    github: {
      clientId: process.env.GITHUB_CLIENT_ID!,
      clientSecret: process.env.GITHUB_CLIENT_SECRET!,
    },
    discord: {
      clientId: process.env.DISCORD_CLIENT_ID!,
      clientSecret: process.env.DISCORD_CLIENT_SECRET!,
    },
  },
});
```

---

## Plugins

Better Auth supporte un système de plugins pour étendre les fonctionnalités.

### Two Factor Authentication (2FA)

```ts
// lib/auth.ts
import { betterAuth } from "better-auth";
import { twoFactor } from "better-auth/plugins";

export const auth = betterAuth({
  plugins: [
    twoFactor({
      issuer: "MonApp",
    }),
  ],
});
```

```ts
// lib/auth-client.ts
import { createAuthClient } from "better-auth/react";
import { twoFactorClient } from "better-auth/client/plugins";

export const authClient = createAuthClient({
  plugins: [twoFactorClient()],
});
```

### Magic Link

```ts
import { betterAuth } from "better-auth";
import { magicLink } from "better-auth/plugins";

export const auth = betterAuth({
  plugins: [
    magicLink({
      sendMagicLink: async ({ email, url }) => {
        // Envoyer l'email avec le lien magique
        await sendEmail({
          to: email,
          subject: "Votre lien de connexion",
          html: `<a href="${url}">Se connecter</a>`,
        });
      },
    }),
  ],
});
```

---

## Middleware de protection

```ts
// middleware.ts
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export async function middleware(request: NextRequest) {
  const sessionCookie = request.cookies.get("better-auth.session_token");

  const protectedRoutes = ["/dashboard", "/profile", "/settings"];
  const isProtectedRoute = protectedRoutes.some((route) =>
    request.nextUrl.pathname.startsWith(route),
  );

  if (isProtectedRoute && !sessionCookie) {
    return NextResponse.redirect(new URL("/login", request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ["/dashboard/:path*", "/profile/:path*", "/settings/:path*"],
};
```
