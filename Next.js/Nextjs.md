# Cheatsheet Next.js

- [Index](../Readme.md)
- [Doc](https://nextjs.org/docs)

## Créer un projet

```bash
npx create-next-app@latest
```

## Structure App Router

```text
app/
├── layout.tsx
├── page.tsx
├── globals.css
├── dashboard/
│   └── page.tsx
├── api/
│   └── users/
│       └── route.ts
└── loading.tsx
```

## Page, layout et route

```tsx
// app/page.tsx
export default function HomePage() {
  return <h1>Accueil</h1>;
}

// app/layout.tsx
export default function RootLayout({ children }) {
  return (
    <html lang="fr">
      <body>{children}</body>
    </html>
  );
}
```

## Server Components vs Client Components

```tsx
// Server Component par défaut
export default async function UsersPage() {
  const res = await fetch("https://api.example.com/users");
  const users = await res.json();

  return <ul>{users.map((u) => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

```tsx
"use client";

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}
```

## Data fetching moderne

```tsx
export default async function Page() {
  const res = await fetch("https://api.example.com/posts", {
    next: { revalidate: 60 },
  });

  const posts = await res.json();

  return <pre>{JSON.stringify(posts, null, 2)}</pre>;
}
```

## Dynamic routes

```tsx
// app/posts/[id]/page.tsx
export default async function PostPage({ params }) {
  const { id } = await params;

  const res = await fetch(`https://api.example.com/posts/${id}`);
  const post = await res.json();

  return <article>{post.title}</article>;
}
```

## Route handlers

```ts
// app/api/users/route.ts
export async function GET() {
  return Response.json({ users: ["Alice", "Bob"] });
}
```

## Server Actions

```tsx
// app/actions.ts
"use server";

export async function createTodo(formData: FormData) {
  const title = formData.get("title");
  // logique serveur
}
```

```tsx
// app/page.tsx
import { createTodo } from "./actions";

export default function Page() {
  return (
    <form action={createTodo}>
      <input name="title" />
      <button type="submit">Ajouter</button>
    </form>
  );
}
```

## Loading, error et not-found

```tsx
// app/loading.tsx
export default function Loading() {
  return <p>Chargement...</p>;
}
```

```tsx
// app/error.tsx
"use client";

export default function Error({ reset }) {
  return (
    <button onClick={() => reset()}>Réessayer</button>
  );
}
```

```tsx
// app/not-found.tsx
export default function NotFound() {
  return <p>Page introuvable</p>;
}
```

## Metadata

```tsx
export const metadata = {
  title: "Mon app",
  description: "Description de la page",
};
```

## Middleware

```ts
// middleware.ts
import { NextResponse } from "next/server";

export function middleware(request) {
  const token = request.cookies.get("token");

  if (!token) {
    return NextResponse.redirect(new URL("/login", request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ["/dashboard/:path*"],
};
```

## Variables d’environnement

```bash
NEXT_PUBLIC_API_URL=http://localhost:3000
DATABASE_URL=postgresql://...
```

```tsx
const apiUrl = process.env.NEXT_PUBLIC_API_URL;
```

## Bonnes pratiques

- Utiliser l’App Router par défaut.
- Préférer les Server Components aux Client Components.
- Les données dynamiques doivent être fetched côté serveur.
- Éviter les `useEffect` pour récupérer des données si le serveur peut le faire.
- Utiliser `revalidate` ou `cache: "no-store"` selon le besoin.
- Garder les layouts, loading et error UI cohérents.

## Éléments récents à connaître

- Next.js 14+ : App Router comme standard.
- `fetch` avec cache/revalidation est central.
- Les Server Actions sont la manière moderne d’écrire des actions serveur.
- Les route handlers remplacent largement les anciennes API routes dans les usages modernes.
