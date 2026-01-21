# Types Placement

Define types where the data is born. **NEVER create a separate `types.ts` file.**

## Why

The first place a type appears is usually the natural home. If it originates in a transport layer or API response, define it there. A centralized `types/` or `types.ts` file loses context and makes types harder to discover.

## Anti-pattern (NEVER DO THIS)

```
feature/
├── types.ts           # ❌ NEVER - types lose context from their source
├── service.server.ts
└── Component.tsx
```

## Correct Pattern

```
feature/
├── service.server.ts  # ✅ Types defined here, alongside functions that create/use them
└── Component.tsx      # ✅ Props interface defined here, with the component
```

## Examples

```ts
// app/features/auth/auth-api.ts
// ✅ CORRECT: API types live with API functions where data originates
export interface SignUpRequest {
  email: string;
  password: string;
}

export interface SignInRequest {
  email: string;
  password: string;
}

export interface AuthResponse {
  token: string;
  user: { email: string };
}

export async function signUp(data: SignUpRequest): Promise<AuthResponse> {
  // ...
}

export async function signIn(data: SignInRequest): Promise<AuthResponse> {
  // ...
}
```

```ts
// app/features/auth/types.ts
// ❌ WRONG: separate types.ts file - types lose context from their data source
export interface SignUpRequest { ... }
export interface SignInRequest { ... }
export interface AuthResponse { ... }
```


```ts
// app/features/notes/notes-api.ts
// ✅ Good: type lives with the data source
export interface Note {
  id: string;
  title: string;
  content: string;
}

export async function fetchNotes(): Promise<Note[]> {
  // ...
}
```

```ts
// app/features/notes/NotesList.tsx
// ✅ CORRECT: component props defined with the component
interface NotesListProps {
  notes: Note[];
  onAdd: () => void;
}

export function NotesList({ notes, onAdd }: NotesListProps) {
  // ...
}
```


```ts
// ❌ Avoid: separate types.ts file with no context
// app/types.ts
export interface Note { ... }
// ...
export interface Props { ... }
// ...
export interface Foo { ... }
// ...
```


## Rule Summary

- Types for API/service functions → define in the service file
- Types for component props → define in the component file
- Types from external sources (Prisma, etc.) → re-export from index.ts if needed
- **NEVER create a standalone `types.ts` file**
