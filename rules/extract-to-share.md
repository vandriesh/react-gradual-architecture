# Logic Extraction

Use hooks for React-dependent logic; use plain functions for everything else.

## Why

Framework-agnostic code is easier to test and reuse. Only wrap logic in a hook when it needs React APIs (state, effects, context).
Avoid a hook for trivial, pure derivations; keep those as plain functions.

## Example

```ts
// app/features/shared/format-date.ts
// ✅ Good: pure function, no React dependency
export function formatDate(date: Date): string {
  return date.toLocaleDateString();
}
```

```ts
// app/features/notes/useNotes.ts
import { useState } from 'react';

// ✅ Good: hook because it uses React state
export function useNotes() {
  const [notes, setNotes] = useState<Note[]>([]);
  // fetch, mutations, etc.
  return { notes };
}
```
