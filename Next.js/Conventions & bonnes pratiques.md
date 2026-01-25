# Conventions & Bonnes Pratiques Next.js

## 📋 Conventions de Nommage

### Fichiers et Dossiers

- **kebab-case** pour les fichiers utilitaires
- **PascalCase** pour les composants React
- Structure App Router (Next.js 13+)

```
app/
├── (marketing)/           # Route groups
│   ├── page.tsx          # /
│   └── about/
│       └── page.tsx      # /about
├── dashboard/
│   ├── layout.tsx
│   ├── page.tsx          # /dashboard
│   └── settings/
│       └── page.tsx      # /dashboard/settings
├── api/
│   └── users/
│       └── route.ts      # API route
├── layout.tsx            # Root layout
└── not-found.tsx         # 404 page

components/
├── ui/
│   ├── Button.tsx
│   └── Card.tsx
└── features/
    └── UserProfile.tsx

lib/
├── utils.ts
├── api-client.ts
└── constants.ts
```

---

## 🏗️ App Router (Next.js 13+)

### Structure des fichiers spéciaux

```typescript
// app/layout.tsx - Root Layout
import { Inter } from 'next/font/google';
import './globals.css';

const inter = Inter({ subsets: ['latin'] });

export const metadata = {
  title: 'My App',
  description: 'Description',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="fr">
      <body className={inter.className}>{children}</body>
    </html>
  );
}

// app/page.tsx - Page
export default function HomePage() {
  return <h1>Home Page</h1>;
}

// app/dashboard/layout.tsx - Nested Layout
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <div>
      <nav>{/* Dashboard nav */}</nav>
      <main>{children}</main>
    </div>
  );
}

// app/loading.tsx - Loading UI
export default function Loading() {
  return <div>Loading...</div>;
}

// app/error.tsx - Error UI
'use client';

export default function Error({
  error,
  reset,
}: {
  error: Error;
  reset: () => void;
}) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <button onClick={reset}>Try again</button>
    </div>
  );
}

// app/not-found.tsx - 404
export default function NotFound() {
  return <h1>404 - Page Not Found</h1>;
}
```

### Route Groups et Parallel Routes

```typescript
// (marketing) - Route group (n'affecte pas l'URL)
app/
├── (marketing)/
│   ├── layout.tsx
│   └── page.tsx          // /
├── (dashboard)/
│   ├── layout.tsx
│   └── page.tsx          // /

// Parallel Routes
app/
├── @modal/
│   └── login/
│       └── page.tsx
├── layout.tsx
└── page.tsx

// app/layout.tsx
export default function Layout({
  children,
  modal,
}: {
  children: React.ReactNode;
  modal: React.ReactNode;
}) {
  return (
    <>
      {children}
      {modal}
    </>
  );
}
```

---

## ✅ Server Components vs Client Components

### Server Components (par défaut)

```typescript
// ✅ Server Component (défaut)
// - Pas d'interactivité
// - Peut fetcher des données directement
// - Meilleur pour SEO
// - Bundle JS plus petit

export default async function UserProfile({ userId }: { userId: string }) {
  // Fetch directement dans le composant
  const user = await fetch(`https://api.example.com/users/${userId}`)
    .then(res => res.json());

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

### Client Components

```typescript
// ✅ Client Component - 'use client' obligatoire
'use client';

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

// ✅ Quand utiliser 'use client' :
// - useState, useEffect, autres hooks React
// - Event handlers (onClick, onChange, etc.)
// - Browser APIs (localStorage, window, etc.)
// - Context providers
```

### Composition Server + Client

```typescript
// ✅ Bonne composition
// app/page.tsx (Server Component)
import ClientComponent from '@/components/ClientComponent';

export default function Page() {
  const serverData = await fetchData();

  return (
    <div>
      <h1>Server Rendered</h1>
      <ClientComponent initialData={serverData} />
    </div>
  );
}

// components/ClientComponent.tsx
'use client';

export default function ClientComponent({ initialData }: any) {
  const [data, setData] = useState(initialData);
  // Logique client
  return <div>{/* UI interactive */}</div>;
}
```

---

## 🔄 Data Fetching

### fetch() avec cache et revalidation

```typescript
// ✅ Cache par défaut (force-cache)
const data = await fetch("https://api.example.com/data");

// Pas de cache
const data = await fetch("https://api.example.com/data", {
  cache: "no-store",
});

// Revalidation après 60 secondes
const data = await fetch("https://api.example.com/data", {
  next: { revalidate: 60 },
});

// Tag pour revalidation on-demand
const data = await fetch("https://api.example.com/data", {
  next: { tags: ["users"] },
});

// Revalidation manuelle
import { revalidateTag } from "next/cache";
revalidateTag("users");
```

### Streaming et Suspense

```typescript
// app/dashboard/page.tsx
import { Suspense } from 'react';

async function UserData() {
  const users = await fetchUsers(); // Slow
  return <div>{/* render users */}</div>;
}

async function QuickStats() {
  const stats = await fetchStats(); // Fast
  return <div>{/* render stats */}</div>;
}

export default function Dashboard() {
  return (
    <div>
      {/* Stats s'affichent immédiatement */}
      <Suspense fallback={<div>Loading stats...</div>}>
        <QuickStats />
      </Suspense>

      {/* Users se chargent après */}
      <Suspense fallback={<div>Loading users...</div>}>
        <UserData />
      </Suspense>
    </div>
  );
}
```

### Server Actions

```typescript
// ✅ Server Actions (Next.js 13.4+)
// app/actions.ts
'use server';

import { revalidatePath } from 'next/cache';

export async function createUser(formData: FormData) {
  const name = formData.get('name');
  const email = formData.get('email');

  // Save to database
  await db.users.create({ name, email });

  // Revalidate
  revalidatePath('/users');

  return { success: true };
}

// app/users/new/page.tsx
import { createUser } from '@/app/actions';

export default function NewUserPage() {
  return (
    <form action={createUser}>
      <input name="name" required />
      <input name="email" type="email" required />
      <button type="submit">Create</button>
    </form>
  );
}

// Ou avec useFormState (client)
'use client';
import { useFormState } from 'react-dom';
import { createUser } from '@/app/actions';

export default function Form() {
  const [state, formAction] = useFormState(createUser, { success: false });

  return (
    <form action={formAction}>
      {/* form */}
      {state.success && <p>User created!</p>}
    </form>
  );
}
```

---

## 🛣️ Routing

### Dynamic Routes

```typescript
// app/blog/[slug]/page.tsx
export default function BlogPost({ params }: { params: { slug: string } }) {
  return <h1>Post: {params.slug}</h1>;
}

// Génération statique
export async function generateStaticParams() {
  const posts = await fetch('https://api.example.com/posts').then(res => res.json());

  return posts.map((post: any) => ({
    slug: post.slug,
  }));
}

// Catch-all routes
// app/shop/[...slug]/page.tsx
// /shop/a → { slug: ['a'] }
// /shop/a/b → { slug: ['a', 'b'] }

// Optional catch-all
// app/shop/[[...slug]]/page.tsx
// /shop → { slug: undefined }
// /shop/a → { slug: ['a'] }
```

### Metadata API

```typescript
// Static metadata
export const metadata = {
  title: "My Page",
  description: "Page description",
  openGraph: {
    title: "My Page",
    description: "Page description",
    images: ["/og-image.jpg"],
  },
};

// Dynamic metadata
export async function generateMetadata({ params }: { params: { id: string } }) {
  const product = await fetch(`/api/products/${params.id}`).then((res) =>
    res.json(),
  );

  return {
    title: product.name,
    description: product.description,
  };
}
```

---

## 🎨 Styling

### CSS Modules

```typescript
// components/Button.module.css
.button {
  padding: 10px 20px;
  background: blue;
}

.button--primary {
  background: green;
}

// components/Button.tsx
import styles from './Button.module.css';

export default function Button({ variant = 'default' }) {
  return (
    <button className={`${styles.button} ${styles[`button--${variant}`]}`}>
      Click me
    </button>
  );
}
```

### Tailwind CSS

```typescript
// ✅ Recommandé avec Next.js
export default function Button() {
  return (
    <button className="px-4 py-2 bg-blue-500 hover:bg-blue-700 text-white rounded">
      Click me
    </button>
  );
}
```

---

## 🔌 API Routes

### Route Handlers (App Router)

```typescript
// app/api/users/route.ts
import { NextResponse } from "next/server";

// GET /api/users
export async function GET(request: Request) {
  const users = await db.users.findMany();
  return NextResponse.json(users);
}

// POST /api/users
export async function POST(request: Request) {
  const body = await request.json();
  const user = await db.users.create({ data: body });
  return NextResponse.json(user, { status: 201 });
}

// app/api/users/[id]/route.ts
export async function GET(
  request: Request,
  { params }: { params: { id: string } },
) {
  const user = await db.users.findUnique({ where: { id: params.id } });

  if (!user) {
    return NextResponse.json({ error: "Not found" }, { status: 404 });
  }

  return NextResponse.json(user);
}

export async function PUT(
  request: Request,
  { params }: { params: { id: string } },
) {
  const body = await request.json();
  const user = await db.users.update({
    where: { id: params.id },
    data: body,
  });

  return NextResponse.json(user);
}

export async function DELETE(
  request: Request,
  { params }: { params: { id: string } },
) {
  await db.users.delete({ where: { id: params.id } });
  return NextResponse.json({ success: true });
}
```

### Middleware

```typescript
// middleware.ts (root)
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  const token = request.cookies.get("token");

  if (!token && request.nextUrl.pathname.startsWith("/dashboard")) {
    return NextResponse.redirect(new URL("/login", request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: "/dashboard/:path*",
};
```

---

## 🚀 Optimisation

### Images

```typescript
import Image from 'next/image';

// ✅ Bon
export default function Avatar() {
  return (
    <Image
      src="/avatar.jpg"
      alt="User avatar"
      width={100}
      height={100}
      quality={75}
      priority // Pour images above the fold
    />
  );
}

// Images externes
// next.config.js
module.exports = {
  images: {
    domains: ['example.com'],
    // Ou remotePatterns (recommandé)
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'example.com',
      },
    ],
  },
};
```

### Fonts

```typescript
// app/layout.tsx
import { Inter, Roboto_Mono } from 'next/font/google';

const inter = Inter({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-inter',
});

const robotoMono = Roboto_Mono({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-roboto-mono',
});

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="fr" className={`${inter.variable} ${robotoMono.variable}`}>
      <body className={inter.className}>{children}</body>
    </html>
  );
}

// Font locale
import localFont from 'next/font/local';

const myFont = localFont({
  src: './my-font.woff2',
  display: 'swap',
});
```

### Code Splitting

```typescript
// ✅ Dynamic imports
import dynamic from 'next/dynamic';

const DynamicComponent = dynamic(() => import('@/components/Heavy'), {
  loading: () => <p>Loading...</p>,
  ssr: false, // Client-only
});

export default function Page() {
  return <DynamicComponent />;
}
```

---

## 🔧 Configuration

### next.config.js

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  // Strict Mode
  reactStrictMode: true,

  // Redirects
  async redirects() {
    return [
      {
        source: "/old-page",
        destination: "/new-page",
        permanent: true,
      },
    ];
  },

  // Rewrites
  async rewrites() {
    return [
      {
        source: "/api/:path*",
        destination: "https://external-api.com/:path*",
      },
    ];
  },

  // Headers
  async headers() {
    return [
      {
        source: "/:path*",
        headers: [
          {
            key: "X-DNS-Prefetch-Control",
            value: "on",
          },
        ],
      },
    ];
  },

  // Images
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "cdn.example.com",
      },
    ],
  },

  // Env variables
  env: {
    CUSTOM_KEY: "value",
  },
};

module.exports = nextConfig;
```

---

## 🔒 Sécurité

### Environment Variables

```bash
# .env.local (ne pas commiter)
DATABASE_URL=postgresql://...
NEXT_PUBLIC_API_URL=https://api.example.com
```

```typescript
// Variables publiques (NEXT_PUBLIC_)
const apiUrl = process.env.NEXT_PUBLIC_API_URL;

// Variables privées (server-side uniquement)
const dbUrl = process.env.DATABASE_URL;
```

### CSRF Protection

```typescript
// middleware.ts
import { csrf } from "@/lib/csrf";

export function middleware(request: NextRequest) {
  return csrf(request);
}
```

---

## 📚 Ressources

- [Next.js Documentation](https://nextjs.org/docs)
- [Next.js Learn](https://nextjs.org/learn)
- [Next.js Examples](https://github.com/vercel/next.js/tree/canary/examples)
- [Vercel Deployment](https://vercel.com/docs)
