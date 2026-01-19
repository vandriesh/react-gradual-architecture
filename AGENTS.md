# React Gradual Architecture

**Version 1.0.0**  
Incremental React Code Organization  
January 2026

> **Note:**  
> This document is mainly for agents and LLMs to follow when maintaining,  
> generating, or refactoring React codebases. Humans may also find it useful,  
> but guidance here is optimized for automation and consistency by AI-assisted workflows.

---

## Abstract

Lightweight guidance for organizing React code without over-structuring. The core idea: start small, minimum necessary (one file), extract only when it improves clarity or reuse. Contains 12 rules across 5 categories, prioritized by when to apply them in the development lifecycle. Each rule includes detailed explanations, real-world examples comparing incorrect vs. correct implementations, and specific guidance on when to extract or reorganize code.

---

## Table of Contents

1. [Foundation](#1-foundation) — **CRITICAL**
   - 1.1 [Follow Framework Conventions](#11-follow-framework-conventions)
2. [Starting](#2-starting) — **CRITICAL**
   - 2.1 [Start Small: Single File](#21-start-small-single-file)
   - 2.2 [Create Components with Composition](#22-create-components-with-composition)
3. [Organization & Structure](#3-organization--structure) — **HIGH**
   - 3.1 [Place Features in Features Folder](#31-place-features-in-features-folder)
   - 3.2 [Keep Related Files Close (Locality)](#32-keep-related-files-close-locality)
   - 3.3 [Wait for Second Use Before Extracting](#33-wait-for-second-use-before-extracting)
   - 3.4 [Create Feature README](#34-create-feature-readme)
4. [Extraction](#4-extraction) — **MEDIUM**
   - 4.1 [Extract When Scanning Becomes Difficult](#41-extract-when-scanning-becomes-difficult)
   - 4.2 [Use Hooks for React Logic, Plain Functions for Everything Else](#42-use-hooks-for-react-logic-plain-functions-for-everything-else)
   - 4.3 [Extract UI, Logic, and Utils When Concerns Diverge](#43-extract-ui-logic-and-utils-when-concerns-diverge)
   - 4.4 [Extract Container from Presentational Components](#44-extract-container-from-presentational-components)
5. [Data & State](#5-data--state) — **MEDIUM**
   - 5.1 [Define Types Where Data is Born](#51-define-types-where-data-is-born)
   - 5.2 [Keep State Local; Lift Only as High as Needed](#52-keep-state-local-lift-only-as-high-as-needed)

---

## 1. Foundation

**Impact: CRITICAL**

Foundation rules apply before starting any feature. They ensure code remains framework-agnostic and follows framework conventions.

### 1.1 Follow Framework Conventions

**Impact: CRITICAL (enables framework portability)**

Follow the framework's official recommendations for structure and best practices. Keep framework glue out of domain code so the app can move across frameworks.

**Rules:**

- Framework conventions win; keep the framework layer standard and doc-aligned
- Avoid framework-only types leaking into domain models or business logic
- Write business logic that works with any UI platform
- Aim for isolation at both levels when possible: framework and platform agnostic
- Write business logic as pure TypeScript with zero framework imports
- If code can be written without framework, do it

**Incorrect: framework types leak into domain**

```typescript
// app/features/auth/auth-api.ts
import { NextRequest } from 'next/server'; // ❌ Framework-specific type

export interface SignUpRequest {
  email: string;
  password: string;
}

export async function signUp(
  request: NextRequest // ❌ Framework dependency
): Promise<AuthResponse> {
  const body = await request.json();
  // ...
}
```

**Correct: framework-agnostic domain code**

```typescript
// app/features/auth/auth-api.ts
// ✅ Good: Pure TypeScript, no framework imports

export interface SignUpRequest {
  email: string;
  password: string;
}

export interface AuthResponse {
  token: string;
  user: { email: string };
}

export async function signUp(
  data: SignUpRequest // ✅ Framework-agnostic
): Promise<AuthResponse> {
  const response = await fetch('/api/auth/signup', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data),
  });
  return response.json();
}
```

**Framework wiring stays thin:**

```typescript
// app/api/auth/signup/route.ts (Next.js API route)
import { signUp } from '@/features/auth/auth-api';
import { NextRequest, NextResponse } from 'next/server';

export async function POST(request: NextRequest) {
  const body = await request.json();
  const result = await signUp(body); // ✅ Domain logic is framework-free
  return NextResponse.json(result);
}
```

**Success Criteria:**

- Swapping frameworks does not force changes in domain logic
- Domain code is testable without framework/platform mocks/APIs

---

## 2. Starting

**Impact: CRITICAL**

Starting rules apply when beginning a new feature. They prevent premature structure and over-engineering.

### 2.1 Start Small: Single File

**Impact: CRITICAL (prevents premature structure)**

Begin a new feature in a single file. Avoid premature folders or separate files until there is a clear reason to extract.

**Why:**

Early structure tends to guess at future needs. A single file keeps the working set small and makes refactoring easy.

**Incorrect: premature folder structure**

```tsx
// ❌ Avoid: creating structure before understanding needs
app/features/auth/
├── components/
│   ├── SignInForm.tsx
│   └── SignUpForm.tsx
├── hooks/
│   └── useAuth.ts
├── utils/
│   └── auth-utils.ts
└── types.ts
```

**Correct: start with single file**

```tsx
// ✅ Good: start small, extract when needed
app / features / auth / Auth.tsx;

function Auth() {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(false);

  const signIn = async (email: string, password: string) => {
    setLoading(true);
    // ... sign in logic ...
  };

  const signUp = async (email: string, password: string) => {
    setLoading(true);
    // ... sign up logic ...
  };

  return (
    <div>
      {user ? (
        <button onClick={() => setUser(null)}>Sign Out</button>
      ) : (
        <>
          <SignInForm onSubmit={signIn} />
          <SignUpForm onSubmit={signUp} />
        </>
      )}
    </div>
  );
}
```

**Extraction threshold:**

- If a file exceeds ~100 lines, consider extraction
- Extract when scanning becomes difficult (see [Extract When Scanning Becomes Difficult](#41-extract-when-scanning-becomes-difficult))
- Extract when concerns diverge (UI vs logic vs utils)

### 2.2 Create Components with Composition

**Impact: CRITICAL (prevents prop bloat and improves flexibility)**

Prefer composition over configuration. Use children and component composition instead of many props. Avoid configuring behavior through props.

**Why:**

Components with many props become hard to use and maintain. Composition provides flexibility and keeps components focused. Configuration props create coupling and reduce reusability.

**Exception:** Dedicated generic props with the purpose of variants (e.g., `variant="primary"`, `size="large"`) are acceptable as they represent visual/behavioral variations of the same component.

**Incorrect: configuring through many props**

```tsx
// ❌ Avoid: too many configuration props
<Card
  showHeader={true}
  showFooter={false}
  headerTitle="Title"
  headerIcon={<Icon />}
  footerActions={[<Button />, <Button />]}
  contentPadding="large"
  contentAlign="center"
  borderStyle="rounded"
  shadowLevel="medium"
/>
```

**Correct: composition**

```tsx
// ✅ Good: composition with children
<Card>
  <Card.Header>
    <Card.Title>Title</Card.Title>
    <Card.Icon>
      <Icon />
    </Card.Icon>
  </Card.Header>
  <Card.Content padding="large" align="center">
    Main content here
  </Card.Content>
  <Card.Footer>
    <Button>Action 1</Button>
    <Button>Action 2</Button>
  </Card.Footer>
</Card>
```

**Correct: variant prop for visual variations**

```tsx
// ✅ Good: variant prop for visual variations
<Button variant="primary" size="large">
  Click me
</Button>

<Card variant="outlined" elevation="medium">
  Content
</Card>
```

**Incorrect: variant prop for behavior configuration**

```tsx
// ❌ Avoid: using variant for behavior configuration
<Modal variant="withSidebarAndFooter">
  Content
</Modal>

// ✅ Good: composition for behavior
<Modal>
  <Modal.Sidebar>Sidebar content</Modal.Sidebar>
  <Modal.Content>Main content</Modal.Content>
  <Modal.Footer>Footer actions</Modal.Footer>
</Modal>
```

---

## 3. Organization & Structure

**Impact: HIGH**

Organization rules apply when structuring files and folders. They ensure features are discoverable and maintainable.

### 3.1 Place Features in Features Folder

**Impact: HIGH (enables feature discovery)**

Place features under `src/features/<feature>/` or `app/features/<feature>/`. Name folders after what the feature does, not what framework artifacts it contains.

**Note:** In monorepo setups, use the monorepo way to add a new lib/package for a new feature (e.g., `packages/<feature>/` or `libs/<feature>/`).

**Why:**

A folder named `hooks/` or `components/` tells you nothing about the domain. A folder named `auth/` or `cart/` screams the feature's purpose at a glance.

**Incorrect: structure that hides intent**

```
src/
├── services/
├── hooks/
├── components/
└── store/
```

**Correct: structure that screams what it does**

```
app/features/auth/
├── Auth.tsx
├── AuthProvider.tsx
├── useAuth.ts
└── auth-api.ts
```

**Note:** Prefer `src/packages/<feature>/` or `src/features/<feature>/` even in single-app repos to stay monorepo-ready. It nudges decoupling and makes a later move to module boundaries smoother.

### 3.2 Keep Related Files Close (Locality)

**Impact: HIGH (reduces cognitive load)**

Keep related files close to the feature they serve. Avoid global folders until reuse forces it.

**Why:**

Locality reduces mental jumps. You can understand a feature by opening its nearby files.

**Incorrect: shared folder without real sharing**

```
/components
/hooks
/utils
```

**Correct: feature-local files**

```
app/features/orders/
├── OrdersView.tsx
├── useOrders.ts
└── orders-utils.ts
```

**When to extract to shared:**

- Wait for second use (see [Wait for Second Use Before Extracting](#33-wait-for-second-use-before-extracting))
- If uses span two related features, extract to `features/shared/`
- Only move to root shared area when reused broadly

### 3.3 Wait for Second Use Before Extracting

**Impact: HIGH (prevents premature abstraction)**

Wait for the second use to decide where shared code should live.

**Note:** The code must be identical (same logic, no extra parameters, matching conditional branches) to qualify as a true "second use" worthy of extraction. Similar but not identical code should remain separate until patterns truly converge.

**Why:**

The first use doesn't reveal the correct scope. The second shows whether the code is feature-local, domain-shared, or app-wide. Extracting too early creates a grab-bag `common/` and makes code harder to navigate.

**Incorrect: premature shared util for single use**

```typescript
// app/shared/formatPrice.ts
// ❌ Extracted too early, only used once
export function formatPrice(value: number) {
  return `$${value.toFixed(2)}`;
}
```

**Correct: extract when second use appears**

```typescript
// app/features/cart/CartTotal.tsx
function CartTotal({ total }: { total: number }) {
  return <span>{formatPrice(total)}</span>;
}

// app/features/invoices/InvoiceTotal.tsx
function InvoiceTotal({ total }: { total: number }) {
  return <strong>{formatPrice(total)}</strong>;
}

// app/features/shared/formatPrice.ts
// ✅ Extracted after second use, placed in closest common ground
export function formatPrice(value: number) {
  return `$${value.toFixed(2)}`;
}
```

**Guidance:**

- If both uses are in the same feature, keep it local
- If uses span two related features, extract to `features/shared/`
- Only move to root shared area when reused broadly

### 3.4 Create Feature README

**Impact: MEDIUM (enables feature understanding)**

Each feature should have a README.md that documents its structure, specifications, and usage.

**Why:**

A feature README serves as living documentation that helps developers understand:

- What the feature does and its purpose
- How the feature is structured (file organization)
- API/specifications (props, hooks, endpoints)
- Usage examples
- Dependencies and requirements

**Structure:**

Place `README.md` at the root of each feature directory:

```
src/features/auth/
├── README.md           # Feature documentation
├── Auth.tsx
├── AuthProvider.tsx
└── SignInForm.tsx
```

**README Content:**

```markdown
# Auth Feature

## Overview

Handles user authentication: sign up, sign in, and sign out. Manages user sessions and provides authentication state to the application.

## Structure
```

auth/
├── AuthProvider.tsx # Context provider, exposes useAuth hook
├── SignInForm.tsx # Sign in form component
├── SignUpForm.tsx # Sign up form component
├── auth-api.ts # API functions and types
└── README.md # This file

```

## API

### Hooks

- `useAuth()` - Returns `{ user, signIn, signUp, signOut, loading, error }`

### Components

- `<AuthProvider>` - Wraps auth-required routes
- `<SignInForm>` - Email/password sign in form
- `<SignUpForm>` - Email/password/confirm sign up form

### Types

- `User` - `{ id: string, email: string }`
- `AuthState` - `{ user: User | null, loading: boolean, error: string | null }`

## Usage

\`\`\`tsx
import { AuthProvider, useAuth } from '@/features/auth'

function App() {
  return (
    <AuthProvider>
      <ProtectedRoutes />
    </AuthProvider>
  )
}

function Dashboard() {
  const { user, signOut } = useAuth()
  return (
    <div>
      <p>Welcome, {user?.email}</p>
      <button onClick={signOut}>Sign Out</button>
    </div>
  )
}
\`\`\`

## Dependencies

- `react-router` - For protected routes
- Backend: `/api/auth/signup`, `/api/auth/signin`, `/api/auth/signout`
```

**Keeping Docs in Sync:**

- Update README.md as part of the same PR/commit that changes the feature code
- Treat README updates as part of the feature work, not separate documentation work

---

## 4. Extraction

**Impact: MEDIUM**

Extraction rules apply when refactoring or extracting code. They guide when and how to split code into separate files.

### 4.1 Extract When Scanning Becomes Difficult

**Impact: MEDIUM (improves readability)**

If you are scrolling to understand one concern, it is time to extract that concern.

**Why:**

When a file exceeds one screen height and requires frequent scrolling, the cognitive load rises. Extract the chunk that makes the page feel crowded.

**Note:** On a typical (27" 2k) vertical monitor (~95 LOC visible without scrolling), files exceeding this height often benefit from extraction.

**Extraction Order:**

1. Consider markup + hooks that can be extracted as a presentation component
2. Consider the biggest chunk that can be extracted first - logic/react hook
3. Utils functions

**Incorrect: file too long to scan (240 lines)**

```tsx
// app/features/profile/UserProfile.tsx
// Before: everything in one file
function UserProfile() {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  // ... more state declarations ...

  useEffect(() => {
    const fetchUser = async () => {
      // ... fetch logic ...
    };
    fetchUser();
  }, [userId]);

  // ... more hooks and handlers ...

  return (
    <div className="user-profile">
      <div className="header">{/* ... header content ... */}</div>
      <div className="tabs">{/* ... tab navigation ... */}</div>
      <div className="content">
        {/* ... conditional content sections ... */}
      </div>
    </div>
  );
}
```

**Correct: extracted presentation component (112 lines)**

```tsx
// app/features/profile/UserProfile.tsx
// After: logic stays, UI extracted
function UserProfile() {
  const [user, setUser] = useState<User | null>(null)
  const [loading, setLoading] = useState(true)
  // ... more state declarations ...

  useEffect(() => {
    const fetchUser = async () => {
      // ... fetch logic ...
    }
    fetchUser()
  }, [userId])

  // ... more hooks and handlers ...

  return (
    <UserProfileView
      user={user}
      loading={loading}
      // ... other props ...
    />
  )
}

// app/features/profile/UserProfileView.tsx
// Extracted: pure presentation component
function UserProfileView({ user, loading, ... }: Props) {
  return (
    <div className="user-profile">
      <div className="header">
        {/* ... header content ... */}
      </div>
      <div className="tabs">
        {/* ... tab navigation ... */}
      </div>
      <div className="content">
        {/* ... conditional content sections ... */}
      </div>
    </div>
  )
}
```

**Result:** File reduced from 240 lines to 112 lines. The long markup section (128 lines) was extracted into `UserProfileView` component, making the file easier to scan and understand.

### 4.2 Use Hooks for React Logic, Plain Functions for Everything Else

**Impact: MEDIUM (improves testability and reuse)**

Use hooks for React-dependent logic; use plain functions for everything else.

**Why:**

Framework-agnostic code is easier to test and reuse. Only wrap logic in a hook when it needs React APIs (state, effects, context). Avoid a hook for trivial, pure derivations; keep those as plain functions.

**Incorrect: hook for pure function**

```typescript
// ❌ Avoid: hook for pure logic
import { useMemo } from 'react';

export function useFormatDate(date: Date): string {
  return useMemo(() => date.toLocaleDateString(), [date]);
}
```

**Correct: plain function**

```typescript
// ✅ Good: pure function, no React dependency
export function formatDate(date: Date): string {
  return date.toLocaleDateString();
}
```

**Correct: hook for React-dependent logic**

```typescript
// ✅ Good: hook because it uses React state
import { useState } from 'react';

export function useNotes() {
  const [notes, setNotes] = useState<Note[]>([]);
  // fetch, mutations, etc.
  return { notes };
}
```

### 4.3 Extract UI, Logic, and Utils When Concerns Diverge

**Impact: MEDIUM (improves separation of concerns)**

When responsibilities diverge, extract UI/presentational pieces from logic and utils.

**Why:**

UI reads better when it is not buried in data fetching or heavy transformation logic. Utilities are easier to test and reuse when they are separate.

**Generic UI vs Domain UI:**

- **Generic UI** (free of domain/logic): Can be grouped/organized into a ui-lib and swapped with another library if it follows common generic naming patterns (e.g., `Button`, `Input`, `Card`). These are reusable, framework-agnostic components.

- **Domain UI**: Cannot be swapped—it's specific to your business domain. Domain UI components are wrappers/enhancers around generic UI that add domain-specific behavior, styling, or logic. Be aware of this distinction when organizing UI components.

**Incorrect: UI and data logic tightly coupled**

```tsx
// app/features/account/AccountSummary.tsx
// Before: UI and data logic mixed
export function AccountSummary({ userId }: { userId: string }) {
  const [data, setData] = useState<Account | null>(null);

  useEffect(() => {
    fetch(`/api/accounts/${userId}`)
      .then((res) => res.json())
      .then(setData);
  }, [userId]);

  return <SummaryCard account={data} />;
}
```

**Correct: UI uses extracted hook**

```tsx
// app/features/account/AccountSummary.tsx
// After: UI separated from data logic
export function AccountSummary({ userId }: { userId: string }) {
  const account = useAccount(userId);
  return <SummaryCard account={account} />;
}

// app/features/account/useAccount.ts
// Extracted: data fetching logic
function fetchAccount(userId: string) {
  return fetch(`/api/accounts/${userId}`).then((res) => res.json());
}

export function useAccount(userId: string) {
  const [data, setData] = useState<Account | null>(null);
  useEffect(() => {
    fetchAccount(userId).then(setData);
  }, [userId]);
  return data;
}
```

### 4.4 Separating Container from Presentational Component

**Impact: MEDIUM (improves reuse and testability)**

When a component mixes data fetching with UI rendering, you can separate these concerns: the container component wires data and callbacks, while the presentational component renders UI from props.

**Why:**

Separating orchestration from display keeps components reusable and testable. Presentational components have no state and no domain knowledge. Only UI state and ui logic (e.g. active tab). Use this extraction when it improves reuse or testability; avoid it for simple components.

**When to extract:**

- When you need to reuse the UI with different data sources
- When you want to test UI separately from data logic
- When the component grows and mixing concerns reduces clarity

**Typical smart component (before extraction):**

```tsx
// app/features/notes/Notes.tsx
// Smart component: data fetching and UI together
// This is fine for simple cases, but extract when you need reuse/testability
export function Notes() {
  const [notes, setNotes] = useState<Note[]>([]);

  useEffect(() => {
    fetchNotes().then(setNotes);
  }, []);

  const addNote = (title: string) => {
    // ... add logic ...
  };

  return (
    <ul>
      {notes.map((n) => (
        <li key={n.id}>{n.title}</li>
      ))}
      <button onClick={() => addNote('New Note')}>Add</button>
    </ul>
  );
}
```

**After extraction: container and presentational separated**

```tsx
// app/features/notes/NotesContainer.tsx
// Container: orchestrates data and actions
import { useNotes } from './useNotes';
import { NotesList } from './NotesList';

export function NotesContainer() {
  const { notes, addNote } = useNotes();
  
  const handleAdd = () => {
    addNote('New Note');
  };
  
  return (
    <>
      <NotesList notes={notes} />
      <button onClick={handleAdd}>Add</button>
    </>
  );
}

// app/features/notes/NotesList.tsx
// Presentational: pure UI
import type { Note } from './types';

interface Props {
  notes: Note[];
}

export function NotesList({ notes }: Props) {
  return (
    <ul>
      {notes.map((n) => (
        <li key={n.id}>{n.title}</li>
      ))}
    </ul>
  );
}
```

---

## 5. Data & State

**Impact: MEDIUM**

Data and state rules apply when managing types and state. They ensure types are discoverable and state is properly scoped.

### 5.1 Define Types Where Data is Born

**Impact: MEDIUM (improves type discoverability)**

Define types where the data is born.

**Why:**

The first place a type appears is usually the natural home. If it originates in a transport layer or API response, define it there. Avoid a centralized `types/` folder that loses context.

**Incorrect: separate types.ts file loses context**

```typescript
// app/features/auth/types.ts
// ❌ Avoid: separate types.ts file - types lose context from their data source
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
```

**Correct: types live with API functions where data originates**

```typescript
// app/features/auth/auth-api.ts
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

**Another example: component props**

```typescript
// app/features/notes/NotesList.tsx
// ✅ Good: component props types live with component
export interface NotesListProps {
  notes: Note[];
  onAdd: () => void;
}

export function NotesList({ notes, onAdd }: NotesListProps) {
  // ...
}
```

### 5.2 Keep State Local; Lift Only as High as Needed

**Impact: MEDIUM (reduces coupling and re-renders)**

Start local with useState; expand cautiously.

**Why:**

Global state adds indirection and coupling. Keep state where it's used. If multiple components need the same data, lift it only as high as necessary—not to the root.

**Guidance:**

- Start with `useState` inside the component
- Lift to a shared parent when siblings need the same state
- Move state down the component tree to avoid unnecessary re-renders
- Use context/provider only for the region that needs it; don't wrap the whole app
- Prefer deriving state over hooks (useState/useEffect)

**Incorrect: state at root when only one component needs it**

```tsx
// app/App.tsx
// ❌ Avoid: global state for local use
function App() {
  const [isExpanded, setIsExpanded] = useState(false);
  return (
    <div>
      <Header />
      <ProductCard isExpanded={isExpanded} onToggle={setIsExpanded} />
      <Footer />
    </div>
  );
}
```

**Correct: start local**

```tsx
// app/features/products/ProductCard.tsx
// ✅ Good: Start local with useState
function ProductCard({ product }: { product: Product }) {
  const [isExpanded, setIsExpanded] = useState(false);
  return (
    <Card expanded={isExpanded} onToggle={() => setIsExpanded(!isExpanded)}>
      <ProductDetails product={product} />
    </Card>
  );
}
```

**Correct: lift to shared parent when siblings need same state**

```tsx
// app/features/products/ProductList.tsx
// ✅ Good: Lift to shared parent when siblings need same state
function ProductList({ products }: { products: Product[] }) {
  const [selectedId, setSelectedId] = useState<string | null>(null);
  return (
    <>
      <ProductFilters selectedId={selectedId} />
      {products.map((p) => (
        <ProductCard
          key={p.id}
          product={p}
          isSelected={p.id === selectedId}
          onSelect={() => setSelectedId(p.id)}
        />
      ))}
    </>
  );
}
```

**Correct: move state down to avoid unnecessary re-renders**

```tsx
// app/features/dashboard/Dashboard.tsx
// ✅ Good: Move state down to avoid unnecessary re-renders
function Dashboard() {
  // ... other state ...
  return (
    <div>
      <Header />
      <ProductList products={products} /> {/* State moved here, not in Dashboard */}
    </div>
  );
}

// app/features/products/ProductList.tsx
function ProductList({ products }: { products: Product[] }) {
  const [selectedId, setSelectedId] = useState<string | null>(null);
  // ... only ProductList and children re-render when selectedId changes
}
```

**Correct: context/provider for region, not whole app**

```tsx
// app/App.tsx
// ✅ Good: Context/provider for region, not whole app
function App() {
  return (
    <AuthProvider>
      <Dashboard />
      <PublicFooter /> {/* Doesn't need auth context */}
    </AuthProvider>
  );
}

// app/features/dashboard/Dashboard.tsx
function Dashboard() {
  return (
    <UserPreferencesProvider>
      {' '}
      {/* Scoped to dashboard region */}
      <DashboardContent />
    </UserPreferencesProvider>
  );
}
```

**Correct: prefer deriving state over hooks**

```tsx
// app/features/cart/CartSummary.tsx
// ✅ Good: Prefer deriving state over hooks
function CartSummary({ items }: { items: CartItem[] }) {
  // Derived state - no useState/useEffect needed
  const total = items.reduce(
    (sum, item) => sum + item.price * item.quantity,
    0
  );
  const itemCount = items.length;

  return (
    <div>
      <p>{itemCount} items</p>
      <p>Total: ${total}</p>
    </div>
  );
}

// ❌ Avoid: Unnecessary useState/useEffect for derived values
function CartSummary({ items }: { items: CartItem[] }) {
  const [total, setTotal] = useState(0);
  useEffect(() => {
    setTotal(items.reduce((sum, item) => sum + item.price * item.quantity, 0));
  }, [items]);
  // ...
}
```

---

## Summary

These rules guide incremental React code organization:

1. **Start Small** - Begin features in a single file, extract only when needed
2. **Organize by Feature** - Place features under `features/<feature>/`, name folders by domain
3. **Keep Related Files Close** - Locality reduces cognitive load
4. **Wait for Second Use** - Don't extract shared code until it's actually reused
5. **Extract When Scanning Hurts** - If scrolling to understand, extract
6. **Separate Concerns** - UI, logic, and utils should be separate when they diverge
7. **Types Where Data Lives** - Define types where data originates, not in central folders
8. **State Local First** - Start with useState, lift only as high as needed

Apply the smallest change that restores clarity. Extract only when it improves readability or enables reuse.

---

## References

1. [React Gradual Architecture SKILL.md](SKILL.md) - Human-readable overview
2. [Individual Rule Files](rules/) - Detailed rule explanations
3. [React Documentation](https://react.dev)
4. [Next.js Documentation](https://nextjs.org/docs)
