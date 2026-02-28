---
layout: post
title: "Design Patterns in TypeScript"
date: 2026-02-28 19:30:15 +0000
categories: typescript patterns
tags: ["design-patterns","typescript","architecture","patterns"]
image: /assets/images/coding.jpg

---


A practical reference to the most useful Gang of Four design patterns with concise TypeScript examples. Each pattern includes a real-world use case and a "when to use" note.


## Overview

Design patterns are reusable solutions to common software problems. They fall into three categories:

- **Creational** — how objects are created
- **Structural** — how objects are composed
- **Behavioral** — how objects communicate

---

## Creational Patterns

### Singleton

Ensure a class has only one instance and provide a global access point.

```ts
class Database {
  private static instance: Database | null = null;
  private constructor(private url: string) {}

  static getInstance(url: string): Database {
    if (!Database.instance) {
      Database.instance = new Database(url);
    }
    return Database.instance;
  }

  query(sql: string) { /* ... */ }
}

const db1 = Database.getInstance("postgres://localhost/mydb");
const db2 = Database.getInstance("postgres://localhost/mydb");
console.log(db1 === db2); // true
```

**When to use:** shared connections (DB, cache), config objects, loggers.
**Caution:** global state makes testing harder — prefer dependency injection where possible.

---

### Factory Method

Define an interface for creating an object, but let subclasses decide which class to instantiate.

```ts
interface Notifier {
  send(to: string, message: string): Promise<void>;
}

class EmailNotifier implements Notifier {
  async send(to: string, msg: string) { /* send email */ }
}

class SlackNotifier implements Notifier {
  async send(to: string, msg: string) { /* post to Slack */ }
}

class SMSNotifier implements Notifier {
  async send(to: string, msg: string) { /* send SMS */ }
}

// Factory function (simpler than class-based factory)
function createNotifier(type: "email" | "slack" | "sms"): Notifier {
  switch (type) {
    case "email": return new EmailNotifier();
    case "slack": return new SlackNotifier();
    case "sms":   return new SMSNotifier();
  }
}

const notifier = createNotifier("email");
await notifier.send("user@example.com", "Welcome!");
```

**When to use:** when the exact type isn't known until runtime, or you want to centralize object construction.

---

### Abstract Factory

Create families of related objects without specifying their concrete classes.

```ts
interface Button  { render(): string; }
interface Input   { render(): string; }

interface UIFactory {
  createButton(): Button;
  createInput(): Input;
}

class WebButton  implements Button { render() { return "<button>"; } }
class WebInput   implements Input  { render() { return "<input>"; }  }

class MobileButton implements Button { render() { return "NativeButton"; } }
class MobileInput  implements Input  { render() { return "NativeInput"; }  }

class WebUIFactory    implements UIFactory {
  createButton() { return new WebButton(); }
  createInput()  { return new WebInput(); }
}

class MobileUIFactory implements UIFactory {
  createButton() { return new MobileButton(); }
  createInput()  { return new MobileInput(); }
}

// Usage: swap the whole family in one line
const factory: UIFactory = isMobile ? new MobileUIFactory() : new WebUIFactory();
const btn = factory.createButton();
const inp = factory.createInput();
```

**When to use:** cross-platform UI components, theme systems, test vs production clients.

---

### Builder

Construct a complex object step-by-step, separating construction from representation.

```ts
interface QueryOptions {
  table: string;
  conditions: string[];
  columns: string[];
  limit?: number;
  offset?: number;
}

class QueryBuilder {
  private opts: QueryOptions = { table: "", conditions: [], columns: ["*"] };

  from(table: string): this        { this.opts.table = table; return this; }
  select(...cols: string[]): this  { this.opts.columns = cols; return this; }
  where(cond: string): this        { this.opts.conditions.push(cond); return this; }
  limit(n: number): this           { this.opts.limit = n; return this; }
  offset(n: number): this          { this.opts.offset = n; return this; }

  build(): string {
    const where  = this.opts.conditions.length
      ? `WHERE ${this.opts.conditions.join(" AND ")}` : "";
    const limit  = this.opts.limit  ? `LIMIT ${this.opts.limit}`   : "";
    const offset = this.opts.offset ? `OFFSET ${this.opts.offset}` : "";
    return `SELECT ${this.opts.columns.join(",")} FROM ${this.opts.table} ${where} ${limit} ${offset}`.trim();
  }
}

const sql = new QueryBuilder()
  .from("users")
  .select("id", "name", "email")
  .where("status = 'active'")
  .where("age > 18")
  .limit(10)
  .offset(20)
  .build();
```

**When to use:** complex objects with many optional parameters, constructing queries, HTTP request builders.

---

### Prototype

Create new objects by cloning an existing object.

```ts
interface Cloneable<T> { clone(): T; }

class User implements Cloneable<User> {
  constructor(
    public name: string,
    public role: string,
    public permissions: string[]
  ) {}

  clone(): User {
    return new User(this.name, this.role, [...this.permissions]);
  }
}

const admin = new User("Alice", "admin", ["read", "write", "delete"]);
const guest = admin.clone();
guest.name = "Bob";
guest.role = "guest";
guest.permissions = ["read"];
```

**When to use:** when object creation is expensive (e.g., DB fetch), or you need many similar objects with slight variations.

---

## Structural Patterns

### Adapter

Convert the interface of a class into another interface that clients expect.

```ts
// Legacy API
class LegacyLogger {
  writeLog(level: string, msg: string) {
    console.log(`[${level.toUpperCase()}] ${msg}`);
  }
}

// Expected interface
interface Logger {
  info(msg: string): void;
  error(msg: string): void;
}

// Adapter
class LegacyLoggerAdapter implements Logger {
  constructor(private legacy: LegacyLogger) {}
  info(msg: string)  { this.legacy.writeLog("info", msg); }
  error(msg: string) { this.legacy.writeLog("error", msg); }
}

const logger: Logger = new LegacyLoggerAdapter(new LegacyLogger());
logger.info("App started");
```

**When to use:** integrating third-party libraries, wrapping legacy code, migrating to a new API.

---

### Decorator

Attach additional responsibilities to an object dynamically without changing its class.

```ts
interface DataStore {
  save(data: string): void;
  load(): string;
}

class FileStore implements DataStore {
  private data = "";
  save(data: string) { this.data = data; }
  load() { return this.data; }
}

// Add compression
class CompressedStore implements DataStore {
  constructor(private inner: DataStore) {}
  save(data: string) { this.inner.save(`compressed(${data})`); }
  load() { return this.inner.load().replace(/^compressed\((.+)\)$/, "$1"); }
}

// Add encryption
class EncryptedStore implements DataStore {
  constructor(private inner: DataStore) {}
  save(data: string) { this.inner.save(`encrypted(${data})`); }
  load() { return this.inner.load().replace(/^encrypted\((.+)\)$/, "$1"); }
}

// Stack decorators
const store: DataStore = new EncryptedStore(new CompressedStore(new FileStore()));
store.save("hello");
store.load(); // "hello"
```

**When to use:** adding caching, logging, auth, compression, or encryption without modifying the original class.

---

### Facade

Provide a simplified interface to a complex subsystem.

```ts
// Complex subsystems
class AuthService    { verifyToken(t: string) { return true; } }
class UserService    { getUser(id: string) { return { id, name: "Alice" }; } }
class AuditLogger    { log(event: string) { console.log("AUDIT:", event); } }
class CacheService   { get(k: string) { return null; } set(k: string, v: unknown) {} }

// Facade — one simple entry point
class ProfileFacade {
  private auth  = new AuthService();
  private users = new UserService();
  private audit = new AuditLogger();
  private cache = new CacheService();

  getProfile(token: string, userId: string) {
    if (!this.auth.verifyToken(token)) throw new Error("Unauthorized");
    const cached = this.cache.get(userId);
    if (cached) return cached;
    const user = this.users.getUser(userId);
    this.cache.set(userId, user);
    this.audit.log(`profile_viewed:${userId}`);
    return user;
  }
}

// Client only talks to the facade
const facade = new ProfileFacade();
const profile = facade.getProfile("my-token", "u1");
```

**When to use:** simplifying complex APIs, creating SDK entry points, reducing coupling between layers.

---

### Proxy

Provide a surrogate that controls access to another object (for lazy loading, caching, access control, logging).

```ts
interface ImageLoader {
  display(): void;
}

class RealImage implements ImageLoader {
  constructor(private url: string) {
    console.log(`Loading image from ${url}...`); // expensive
  }
  display() { console.log(`Displaying ${this.url}`); }
}

class LazyImageProxy implements ImageLoader {
  private real: RealImage | null = null;
  constructor(private url: string) {}

  display() {
    if (!this.real) this.real = new RealImage(this.url);  // load on demand
    this.real.display();
  }
}

const img = new LazyImageProxy("https://example.com/large.jpg");
// Image not loaded yet
img.display(); // loads and displays
img.display(); // just displays (cached)
```

**When to use:** lazy initialization, caching, access control, rate limiting, virtual proxies.

---

### Composite

Compose objects into tree structures to represent part-whole hierarchies.

```ts
interface FileSystemNode {
  name: string;
  size(): number;
  print(indent?: string): void;
}

class File implements FileSystemNode {
  constructor(public name: string, private _size: number) {}
  size() { return this._size; }
  print(indent = "") { console.log(`${indent}📄 ${this.name} (${this._size}B)`); }
}

class Folder implements FileSystemNode {
  private children: FileSystemNode[] = [];
  constructor(public name: string) {}
  add(node: FileSystemNode) { this.children.push(node); return this; }
  size() { return this.children.reduce((sum, c) => sum + c.size(), 0); }
  print(indent = "") {
    console.log(`${indent}📁 ${this.name}`);
    this.children.forEach(c => c.print(indent + "  "));
  }
}

const root = new Folder("root")
  .add(new File("readme.md", 1024))
  .add(
    new Folder("src")
      .add(new File("index.ts", 2048))
      .add(new File("utils.ts", 512))
  );

root.print();
console.log("Total:", root.size(), "bytes");
```

---

## Behavioral Patterns

### Observer

Define a one-to-many dependency so that when one object changes state, all dependents are notified.

```ts
type Listener<T> = (event: T) => void;

class EventEmitter<Events extends Record<string, unknown>> {
  private listeners = new Map<keyof Events, Set<Listener<unknown>>>();

  on<K extends keyof Events>(event: K, fn: Listener<Events[K]>): () => void {
    if (!this.listeners.has(event)) this.listeners.set(event, new Set());
    this.listeners.get(event)!.add(fn as Listener<unknown>);
    return () => this.listeners.get(event)?.delete(fn as Listener<unknown>);
  }

  emit<K extends keyof Events>(event: K, payload: Events[K]) {
    this.listeners.get(event)?.forEach(fn => fn(payload));
  }
}

// Usage
type AppEvents = { login: { userId: string }; logout: { userId: string } };
const emitter = new EventEmitter<AppEvents>();

const off = emitter.on("login", ({ userId }) => console.log("Login:", userId));
emitter.emit("login", { userId: "u1" });  // Login: u1
off(); // unsubscribe
```

**When to use:** event systems, state change notifications, reactive data flows.

---

### Strategy

Define a family of algorithms, encapsulate each one, and make them interchangeable at runtime.

```ts
interface SortStrategy<T> {
  sort(data: T[]): T[];
}

class BubbleSort<T> implements SortStrategy<T> {
  sort(data: T[]): T[] { return [...data].sort(); /* simplified */ }
}

class QuickSort<T> implements SortStrategy<T> {
  sort(data: T[]): T[] { return [...data].sort(); /* different impl */ }
}

class Sorter<T> {
  constructor(private strategy: SortStrategy<T>) {}
  setStrategy(s: SortStrategy<T>) { this.strategy = s; }
  sort(data: T[]) { return this.strategy.sort(data); }
}

const sorter = new Sorter(new BubbleSort());
sorter.sort([3, 1, 2]);
sorter.setStrategy(new QuickSort());  // swap at runtime
```

**When to use:** payment methods, compression algorithms, discount calculations, routing logic.

---

### Command

Encapsulate a request as an object, enabling undo/redo, queuing, and logging.

```ts
interface Command {
  execute(): void;
  undo(): void;
}

class TextEditor {
  private content = "";
  getContent() { return this.content; }
  insert(text: string) { this.content += text; }
  delete(count: number) { this.content = this.content.slice(0, -count); }
}

class InsertCommand implements Command {
  constructor(private editor: TextEditor, private text: string) {}
  execute() { this.editor.insert(this.text); }
  undo()    { this.editor.delete(this.text.length); }
}

class CommandHistory {
  private history: Command[] = [];
  execute(cmd: Command) { cmd.execute(); this.history.push(cmd); }
  undo() { this.history.pop()?.undo(); }
}

const editor  = new TextEditor();
const history = new CommandHistory();

history.execute(new InsertCommand(editor, "Hello"));
history.execute(new InsertCommand(editor, ", World"));
console.log(editor.getContent()); // Hello, World
history.undo();
console.log(editor.getContent()); // Hello
```

**When to use:** undo/redo, transaction queues, macro recording, task scheduling.

---

### Chain of Responsibility

Pass a request along a chain of handlers until one of them handles it.

```ts
interface Middleware<T> {
  setNext(handler: Middleware<T>): Middleware<T>;
  handle(request: T): T | null;
}

abstract class BaseMiddleware<T> implements Middleware<T> {
  private next: Middleware<T> | null = null;
  setNext(handler: Middleware<T>) { this.next = handler; return handler; }
  handle(request: T): T | null {
    return this.next ? this.next.handle(request) : null;
  }
}

interface HttpRequest { path: string; token?: string; role?: string; }

class AuthMiddleware extends BaseMiddleware<HttpRequest> {
  handle(req: HttpRequest) {
    if (!req.token) { console.log("401 Unauthorized"); return null; }
    return super.handle(req);
  }
}

class RoleMiddleware extends BaseMiddleware<HttpRequest> {
  handle(req: HttpRequest) {
    if (req.role !== "admin") { console.log("403 Forbidden"); return null; }
    return super.handle(req);
  }
}

class RouteHandler extends BaseMiddleware<HttpRequest> {
  handle(req: HttpRequest) {
    console.log(`Handling ${req.path}`);
    return req;
  }
}

// Build the chain
const auth = new AuthMiddleware();
auth.setNext(new RoleMiddleware()).setNext(new RouteHandler());

auth.handle({ path: "/admin", token: "abc", role: "admin" }); // Handling /admin
auth.handle({ path: "/admin" });                               // 401 Unauthorized
```

**When to use:** middleware pipelines, event handling, validation chains, logging interceptors.

---

### Template Method

Define the skeleton of an algorithm in a base class, deferring some steps to subclasses.

```ts
abstract class DataExporter {
  // Template method — defines the algorithm
  export(data: unknown[]) {
    const validated = this.validate(data);
    const transformed = this.transform(validated);
    const output = this.format(transformed);
    this.write(output);
  }

  protected validate(data: unknown[]) { return data.filter(Boolean); }

  protected abstract transform(data: unknown[]): unknown[];
  protected abstract format(data: unknown[]): string;
  protected abstract write(output: string): void;
}

class CSVExporter extends DataExporter {
  protected transform(data: unknown[]) { return data; }
  protected format(data: unknown[]) {
    return data.map(row => Object.values(row as object).join(",")).join("\n");
  }
  protected write(output: string) { console.log("Writing CSV:", output); }
}
```

**When to use:** report generation, ETL pipelines, test lifecycle hooks.

---

## Quick pattern reference

| Pattern | Category | One-liner |
|---|---|---|
| Singleton | Creational | One instance, global access |
| Factory Method | Creational | Create objects via an interface |
| Abstract Factory | Creational | Create families of related objects |
| Builder | Creational | Step-by-step complex construction |
| Prototype | Creational | Clone existing objects |
| Adapter | Structural | Convert incompatible interfaces |
| Decorator | Structural | Add behavior dynamically |
| Facade | Structural | Simplify a complex subsystem |
| Proxy | Structural | Control access to an object |
| Composite | Structural | Treat single and group objects uniformly |
| Observer | Behavioral | Notify many when one changes |
| Strategy | Behavioral | Swap algorithms at runtime |
| Command | Behavioral | Encapsulate requests as objects |
| Chain of Responsibility | Behavioral | Pass requests through a handler chain |
| Template Method | Behavioral | Algorithm skeleton, steps deferred |

Further reading: *Design Patterns* by Gang of Four, *Head First Design Patterns*, and [refactoring.guru/design-patterns](https://refactoring.guru/design-patterns).
