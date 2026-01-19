# Types Placement

Define types where the data is born.

## Why

The first place a type appears is usually the natural home. If it originates in a transport layer or API response, define it there. Avoid a centralized `types/` folder that loses context.

## Example

```ts
// app/features/auth/api.ts
// ✅ Good: API types live with API functions where data originates
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
// ❌ Avoid: separate types.ts file - types lose context from their data source
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
// ❌ Avoid: separate types.ts file with no context
// app/types.ts
export interface Note { ... }
// ...
export interface Props { ... }
// ...
export interface Foo { ... }
// ...
```
