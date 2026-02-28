---
layout: post
title: "TypeScript Tips & Tricks"
date: 2026-02-28 19:30:15 +0000
categories: typescript javascript
tags: ["typescript","cheatsheet","javascript","tips"]
image: /assets/images/typescript.jpg
---

A practical collection of TypeScript patterns, lesser-known features, and everyday tips — from basic to advanced.


## tsconfig.json essentials

Start with a strict, modern config:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "lib": ["ES2022"],
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "exactOptionalPropertyTypes": true,
    "forceConsistentCasingInFileNames": true,
    "skipLibCheck": true,
    "outDir": "dist",
    "rootDir": "src",
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true
  }
}
```

Key flags to know:
- `strict` — enables `strictNullChecks`, `strictFunctionTypes`, `noImplicitAny`, etc. Always enable.
- `noUncheckedIndexedAccess` — array/object access returns `T | undefined`. Catches index bugs.
- `exactOptionalPropertyTypes` — differentiates `{ a?: string }` from `{ a: string | undefined }`.

---

## Type vs Interface — when to use which

```ts
// Use interface for object shapes and public APIs (extendable, better error messages)
interface User {
  id: number;
  name: string;
}
interface Admin extends User {
  role: "admin";
}

// Use type for unions, intersections, mapped types, and aliases
type Status = "active" | "inactive" | "banned";
type ID = string | number;
type AdminUser = User & { role: "admin" };

// Interfaces can merge (declaration merging); types cannot
interface Window { myLib: string; }   // augments existing Window type
```

---

## Utility types

TypeScript ships with powerful built-in utilities:

```ts
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
}

// Pick / Omit
type PublicUser = Omit<User, "password">;
type UserPreview = Pick<User, "id" | "name">;

// Partial / Required / Readonly
type DraftUser = Partial<User>;          // all fields optional
type StrictUser = Required<User>;        // all fields required
type FrozenUser = Readonly<User>;        // all fields read-only

// Record
type RoleMap = Record<"admin" | "editor" | "viewer", string[]>;

// ReturnType / Parameters / InstanceType
function fetchUser(id: number): Promise<User> { /* ... */ }
type FetchResult = Awaited<ReturnType<typeof fetchUser>>;  // User
type FetchArgs = Parameters<typeof fetchUser>;             // [number]

// NonNullable
type MaybeUser = User | null | undefined;
type DefiniteUser = NonNullable<MaybeUser>;   // User

// Extract / Exclude
type Events = "click" | "hover" | "focus" | "blur";
type MouseEvents = Extract<Events, "click" | "hover">;    // "click" | "hover"
type NonMouse  = Exclude<Events, "click" | "hover">;      // "focus" | "blur"
```

---

## Template literal types

```ts
type Direction = "top" | "right" | "bottom" | "left";
type Padding = `padding-${Direction}`;
// → "padding-top" | "padding-right" | "padding-bottom" | "padding-left"

type EventName<T extends string> = `on${Capitalize<T>}`;
type ClickEvent = EventName<"click">;   // "onClick"

// Combine with keyof
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};
type UserGetters = Getters<{ name: string; age: number }>;
// → { getName: () => string; getAge: () => number }
```

---

## Discriminated unions

The pattern for modelling states safely:

```ts
type ApiState<T> =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "success"; data: T }
  | { status: "error"; error: Error };

function render<T>(state: ApiState<T>) {
  switch (state.status) {
    case "idle":    return "Idle";
    case "loading": return "Loading...";
    case "success": return JSON.stringify(state.data);   // data is T here
    case "error":   return state.error.message;          // error is Error here
  }
}
```

Exhaustiveness check — TypeScript will warn if you miss a case:

```ts
function assertNever(x: never): never {
  throw new Error(`Unhandled case: ${JSON.stringify(x)}`);
}

function render<T>(state: ApiState<T>): string {
  switch (state.status) {
    case "idle":    return "Idle";
    case "loading": return "Loading...";
    case "success": return String(state.data);
    case "error":   return state.error.message;
    default:        return assertNever(state);  // compile error if a case is missing
  }
}
```

---

## Generics — practical patterns

```ts
// Generic function
function first<T>(arr: T[]): T | undefined {
  return arr[0];
}

// Constrained generics
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

// Generic with default
type ApiResponse<T = unknown> = { data: T; status: number };

// Conditional types
type IsArray<T> = T extends unknown[] ? true : false;
type Flatten<T> = T extends Array<infer Item> ? Item : T;
type Inner = Flatten<string[]>;   // string

// Infer in practice
type UnpackPromise<T> = T extends Promise<infer R> ? R : T;
type Result = UnpackPromise<Promise<User>>;   // User
```

---

## Type guards & narrowing

```ts
// typeof
function double(x: string | number) {
  if (typeof x === "string") return x.repeat(2);
  return x * 2;
}

// instanceof
function handleError(e: unknown) {
  if (e instanceof Error) console.error(e.message);
}

// Custom type guard
function isUser(obj: unknown): obj is User {
  return (
    typeof obj === "object" &&
    obj !== null &&
    "id" in obj &&
    typeof (obj as User).id === "number"
  );
}

// Assertion function (throws if condition fails)
function assertUser(obj: unknown): asserts obj is User {
  if (!isUser(obj)) throw new Error("Not a User");
}

// "in" narrowing
type Cat = { meow(): void };
type Dog = { bark(): void };
function makeSound(animal: Cat | Dog) {
  if ("meow" in animal) animal.meow();
  else animal.bark();
}
```

---

## Mapped types

```ts
// Make all properties nullable
type Nullable<T> = { [K in keyof T]: T[K] | null };

// Make specific keys required
type WithRequired<T, K extends keyof T> = T & Required<Pick<T, K>>;
type UserWithEmail = WithRequired<Partial<User>, "email">;

// Remap keys
type Prefixed<T> = {
  [K in keyof T as `data_${string & K}`]: T[K];
};

// Filter keys by value type
type StringKeys<T> = {
  [K in keyof T]: T[K] extends string ? K : never;
}[keyof T];
type StringFields = StringKeys<User>;   // "name" | "email" | "password"
```

---

## Satisfies operator (TypeScript 4.9+)

```ts
// Without satisfies: you lose the narrow type
const palette = {
  red: [255, 0, 0],
  green: "#00ff00",
} as Record<string, string | number[]>;
palette.red.map(v => v);   // Error: string | number[] has no map

// With satisfies: validates shape but preserves literal types
const palette2 = {
  red: [255, 0, 0],
  green: "#00ff00",
} satisfies Record<string, string | number[]>;
palette2.red.map(v => v);    // ✓ TypeScript knows it's number[]
palette2.green.toUpperCase(); // ✓ TypeScript knows it's string
```

---

## const, as const, and enums

```ts
// as const — narrow all values to literals
const config = {
  host: "localhost",
  port: 5432,
} as const;
// type: { readonly host: "localhost"; readonly port: 5432 }

// Derive union from const array
const ROLES = ["admin", "editor", "viewer"] as const;
type Role = (typeof ROLES)[number];   // "admin" | "editor" | "viewer"

// Prefer const objects over enums
const Direction = {
  Up:    "UP",
  Down:  "DOWN",
  Left:  "LEFT",
  Right: "RIGHT",
} as const;
type Direction = (typeof Direction)[keyof typeof Direction];
// → "UP" | "DOWN" | "LEFT" | "RIGHT"
// Safer than enums: tree-shakeable, serializable, no runtime surprises
```

---

## Decorators (TypeScript 5.0 / stage 3)

```ts
// Method decorator
function log(target: unknown, context: ClassMethodDecoratorContext) {
  return function (this: unknown, ...args: unknown[]) {
    console.log(`Calling ${String(context.name)} with`, args);
    return (target as Function).apply(this, args);
  };
}

class OrderService {
  @log
  placeOrder(orderId: string) { /* ... */ }
}
```

---

## Useful patterns

Branded/nominal types (prevent mixing compatible primitive types):

```ts
type UserId  = string & { readonly __brand: "UserId" };
type OrderId = string & { readonly __brand: "OrderId" };

function toUserId(id: string): UserId { return id as UserId; }

function getUser(id: UserId) { /* ... */ }
const uid = toUserId("abc-123");
getUser(uid);          // ✓
getUser("abc-123");    // Error: string is not UserId
```

Builder pattern with generics:

```ts
class QueryBuilder<T extends Record<string, unknown>> {
  private filters: Partial<T> = {};
  where<K extends keyof T>(key: K, value: T[K]): this {
    this.filters[key] = value;
    return this;
  }
  build(): Partial<T> { return this.filters; }
}
```

Strongly-typed EventEmitter:

```ts
type EventMap = {
  login:  { userId: string };
  logout: { userId: string };
  error:  { message: string };
};

type EventEmitter<T extends Record<string, unknown>> = {
  on<K extends keyof T>(event: K, handler: (payload: T[K]) => void): void;
  emit<K extends keyof T>(event: K, payload: T[K]): void;
};
```

---

## Everyday tips

```ts
// Avoid non-null assertion (!) — use narrowing instead
const el = document.getElementById("app");
if (!el) throw new Error("#app not found");
el.innerHTML = "Hello";   // el is HTMLElement here

// Optional chaining & nullish coalescing
const city = user?.address?.city ?? "Unknown";
const len = str?.length ?? 0;

// Object spread for immutable updates
const updated = { ...user, name: "New Name" };

// Array helpers with type safety
const ids = users.map(u => u.id);                     // number[]
const admins = users.filter((u): u is Admin => u.role === "admin"); // Admin[]
const found = users.find(u => u.id === 1);            // User | undefined

// keyof typeof for objects
const COLORS = { red: "#f00", green: "#0f0", blue: "#00f" } as const;
type ColorName = keyof typeof COLORS;   // "red" | "green" | "blue"

// Infer generics from usage
const wrap = <T>(value: T) => ({ value });   // inferred T, no annotation needed
const w = wrap(42);   // { value: number }
```

---

## Quick checklist

- Enable `strict: true` and `noUncheckedIndexedAccess` in tsconfig.
- Avoid `any` — use `unknown` and narrow it.
- Prefer discriminated unions over optional fields for state modelling.
- Prefer `as const` objects over `enum`.
- Use utility types (`Partial`, `Pick`, `Omit`, etc.) instead of rewriting shapes.
- Use `unknown` for caught errors: `catch (e: unknown)`.
- Avoid non-null assertions (`!`) — prefer explicit narrowing.
- Run `tsc --noEmit` in CI to catch type errors without outputting JS.

That's it — keep this as your TypeScript quick reference and extend with project-specific patterns.
