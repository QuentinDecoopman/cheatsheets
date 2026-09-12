# Cheatsheet React

- [Index](../Readme.md)
- [Doc](https://react.dev/)

## Créer un projet

```bash
npm create vite@latest my-app -- --template react
cd my-app
npm install
```

## Composant fonctionnel

```jsx
function Greeting({ name }) {
  return <h1>Bonjour {name} 👋</h1>;
}
```

## Props

```jsx
function Card({ title, description }) {
  return (
    <article>
      <h2>{title}</h2>
      <p>{description}</p>
    </article>
  );
}
```

## State

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount((c) => c + 1)}>
      Compteur : {count}
    </button>
  );
}
```

## Effets (useEffect)

```jsx
import { useEffect, useState } from "react";

function UserPanel() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch("/api/user")
      .then((res) => res.json())
      .then(setUser);
  }, []);

  return <div>{user ? user.name : "Chargement..."}</div>;
}
```

## Rendu conditionnel

```jsx
function Status({ isOnline }) {
  return isOnline ? <span>En ligne</span> : <span>Hors ligne</span>;
}
```

## Listes et clés

```jsx
function List({ items }) {
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>{item.label}</li>
      ))}
    </ul>
  );
}
```

## Formulaires contrôlés

```jsx
import { useState } from "react";

function SearchForm() {
  const [query, setQuery] = useState("");

  return (
    <form>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Rechercher"
      />
    </form>
  );
}
```

## Hooks utiles

### useMemo

```jsx
import { useMemo, useState } from "react";

function ExpensiveList({ items }) {
  const sorted = useMemo(
    () => [...items].sort((a, b) => a.localeCompare(b)),
    [items],
  );

  return <ul>{sorted.map((item) => <li key={item}>{item}</li>)}</ul>;
}
```

### useCallback

```jsx
import { useCallback, useState } from "react";

function Parent() {
  const [count, setCount] = useState(0);

  const increment = useCallback(() => {
    setCount((c) => c + 1);
  }, []);

  return <Child onClick={increment} />;
}
```

### useRef

```jsx
import { useRef } from "react";

function InputFocus() {
  const inputRef = useRef(null);

  return (
    <>
      <input ref={inputRef} />
      <button onClick={() => inputRef.current?.focus()}>Focus</button>
    </>
  );
}
```

## Requêtes HTTP

```jsx
async function loadUsers() {
  const res = await fetch("https://api.example.com/users");

  if (!res.ok) {
    throw new Error("Erreur API");
  }

  return res.json();
}
```

## Custom Hook

```jsx
import { useEffect, useState } from "react";

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    let ignore = false;

    fetch(url)
      .then((res) => res.json())
      .then((json) => {
        if (!ignore) setData(json);
      })
      .finally(() => {
        if (!ignore) setLoading(false);
      });

    return () => {
      ignore = true;
    };
  }, [url]);

  return { data, loading };
}
```

## Bonnes pratiques

- Préférer les composants fonctionnels et les hooks.
- Garder les composants petits et réutilisables.
- Éviter les effets pour des transformations de données simples.
- Utiliser les clés stables dans les listes.
- Séparer logique métier et rendu visuel.
- Privilégier `useMemo`/`useCallback` seulement si nécessaire.

## Points React récents

- React 18+ : concurrent rendering et `useTransition`.
- `useDeferredValue` et `startTransition` pour améliorer la fluidité.
- Le rendu côté serveur devient standard avec Next.js / React Server Components.
