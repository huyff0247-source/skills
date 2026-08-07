---
name: typescript
description: TypeScript best practices: strict mode, generics, discriminated unions, error handling. Use when writing TypeScript, JavaScript with types, or Node.js.
trigger: \bTypeScript\b ;; \bJavaScript\b ;; \bNode\.js\b ;; \bJSX\b ;; \bgeneric\b ;; \binterface\b ;; \b\.ts\b ;; \b\.tsx\b ;; \bconst\b ;; \blet\b ;; \bvar\b ;; \bES6\b ;; \bES202\d\b ;; \bTypeScript code\b ;; \bTypeScript function\b ;; \bTypeScript class\b ;; \bTypeScript type\b ;; \bviết TypeScript\b
---

# TypeScript Best Practices (Compact)

## Strict Mode (Non-negotiable)
```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,                    // enables all strict checks
    "noUncheckedIndexedAccess": true,  // arr[i] → T | undefined
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true
  }
}
```

## Types over Interfaces (default)
```typescript
// ✅ Type — composable, can be unions/intersections
type User = {
  id: string;
  name: string;
  role: "admin" | "user";
};

// ✅ Interface — only when you need declaration merging
interface Plugin {
  name: string;
  apply(): void;
}

// Discriminated union — the TypeScript superpower
type Result<T> =
  | { ok: true; data: T }
  | { ok: false; error: string };

function handle(result: Result<User>) {
  if (result.ok) {
    result.data; // User — narrowed!
  } else {
    result.error; // string
  }
}
```

## Error Handling
```typescript
// ✅ Typed errors
class AppError extends Error {
  constructor(
    message: string,
    public code: string,
    public status: number = 400
  ) {
    super(message);
    this.name = "AppError";
  }
}

// ✅ Never throw raw strings or unknown errors
throw new AppError("User not found", "NOT_FOUND", 404);

// ✅ Async wrapper — avoid try/catch pyramids
async function safeAsync<T>(promise: Promise<T>): Promise<Result<T>> {
  try {
    return { ok: true, data: await promise };
  } catch (e) {
    return { ok: false, error: e instanceof Error ? e.message : "Unknown error" };
  }
}
```

## Patterns
```typescript
// Branded types — prevent mixing IDs
type UserId = string & { __brand: "UserId" };
type OrderId = string & { __brand: "OrderId" };

function getUser(id: UserId) { ... }
getUser("abc" as UserId); // ✅ explicit
// getUser("abc"); // ❌ TypeScript error

// Zod for runtime validation
import { z } from "zod";
const OrderSchema = z.object({
  userId: z.string().uuid(),
  items: z.array(z.string()).min(1),
  quantity: z.number().int().positive(),
});
type Order = z.infer<typeof OrderSchema>; // type from schema!
```

## Anti-patterns
- `any` — defeats the purpose. Use `unknown` + type guard.
- `as` cast without validation — lie to the compiler, get runtime bugs.
- `enum` — use `as const` or string union instead (smaller bundle, no reverse mapping).
- `Promise<void>` with no `.catch()` — unhandled rejection.
- `export default` — kills IDE autocompletion. Named exports only.