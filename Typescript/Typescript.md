# Cheatsheet TypeScript

- [Index](../Readme.md)
- [Doc](https://www.typescriptlang.org/)

## Types de base

```ts
const count: number = 3;
const name: string = "Alice";
const isActive: boolean = true;
const list: string[] = ["a", "b"];
const tuple: [string, number] = ["id", 1];
```

## Interfaces et types

```ts
interface User {
  id: number;
  name: string;
  email?: string;
}

type UserDTO = {
  id: number;
  name: string;
};
```

## Unions et intersections

```ts
type Status = "idle" | "loading" | "success" | "error";

type AdminUser = User & {
  role: "admin";
};
```

## Generics

```ts
function identity<T>(value: T): T {
  return value;
}

const id = identity<number>(42);
```

## Type narrowing

```ts
function print(value: string | number) {
  if (typeof value === "string") {
    console.log(value.toUpperCase());
    return;
  }

  console.log(value.toFixed(2));
}
```

## `as const` et littéraux

```ts
const roles = ["admin", "user"] as const;

type Role = (typeof roles)[number];
```

## Utility types

```ts
type UserPreview = Pick<User, "id" | "name">;
type UserInput = Omit<User, "id">;
type PartialUser = Partial<User>;
type RequiredUser = Required<User>;
type UserMap = Record<string, User>;
```

## `satisfies`

```ts
const config = {
  apiUrl: "/api",
  retries: 3,
} satisfies {
  apiUrl: string;
  retries: number;
};
```

## `keyof`, `typeof` et mapped types

```ts
type UserKey = keyof User;

const user = { id: 1, name: "Alice" };
type UserShape = typeof user;
```

## Classes

```ts
class Person {
  constructor(public name: string) {}

  greet() {
    return `Bonjour ${this.name}`;
  }
}
```

## Enums (à utiliser avec modération)

```ts
enum Role {
  Admin = "admin",
  User = "user",
}
```

## Fonctions async

```ts
async function fetchUser(id: number): Promise<User> {
  const res = await fetch(`/api/users/${id}`);
  return res.json();
}
```

## Guardes de type personnalisées

```ts
function isUser(value: unknown): value is User {
  return !!value && typeof value === "object" && "id" in value && "name" in value;
}
```

## Bonnes pratiques

- Éviter `any` quand possible.
- Privilégier les interfaces pour les objets de données.
- Utiliser `type` pour des unions, intersections et utilitaires.
- Utiliser `satisfies` pour valider une configuration sans la déduire en `any`.
- Gérer les valeurs `null` / `undefined` explicitement.
- Ajouter des types retour aux fonctions publiques.
