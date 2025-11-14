# Cheatsheet Typescript

- [Index](/Readme.md)
- [Doc](https://www.typescriptlang.org/)

## Déclarations

```typescript
let name: type = value;

const name: type = value;
```

## Types

```typescript
let num: number = 5;

let str: string = "Hello";

let bool: boolean = true;

let arr: number[] = [1, 2, 3];

let tuple: [string, number] = ["foo", 42];
```

## Fonctions

```typescript
function add(x: number, y: number): number {
  return x + y;
}

const multiply = (x: number, y: number): number => x * y;
```

## Interfaces

```typescript
interface Person {
  name: string;
  age: number;
}

const user: Person = {
  name: "John",
  age: 25,
};
```

## Classes

```typescript
class Animal {
  constructor(public name: string) {}

  makeSound(): void {
    console.log("Some generic sound");
  }
}

class Dog extends Animal {
  makeSound(): void {
    console.log("Bark!");
  }
}
```

## Enums

```typescript
enum Color {
  Red,
  Green,
  Blue,
}

let myColor: Color = Color.Red;
```

## Generics

```typescript
function identity<T>(arg: T): T {
  return arg;
}

const numIdentity = identity<number>(5);
```

## Assertions de types

```typescript
let someValue: any = "hello";

let strLength: number = (someValue as string).length;
```

## Modules

```typescript
// Export
export const myVariable: number = 42;

// Import
import { myVariable } from "./myModule";
```

## Types d'alias

```typescript
type Point = {
  x: number;
  y: number;
};

const point: Point = { x: 10, y: 20 };
```

## Types null

```typescript
let nullableNumber: number | null = null;
```

## Gardes

```typescript
if (typeof variable === "string") {
  // variable is a string here
}
