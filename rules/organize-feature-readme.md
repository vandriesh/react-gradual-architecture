# Feature README

Each feature should have a README.md that documents its structure, specifications, and usage.

## Why

A feature README serves as living documentation that helps developers understand:
- What the feature does and its purpose
- How the feature is structured (file organization)
- API/specifications (props, hooks, endpoints)
- Usage examples
- Dependencies and requirements

Keeping README in sync with code ensures documentation stays accurate and helps prevent drift between docs and implementation.

## Structure

Place `README.md` at the root of each feature directory:

Example:
```
src/features/auth
├── README.md           # Feature documentation
├── Auth.tsx
├── AuthProvider.tsx
├── ...
└── SignInForm.tsx
```

## README Content

A feature README should include:

### 1. Overview
- What the feature does
- Purpose and use cases

### 2. Structure
- File organization within the feature
- Brief description of each file's responsibility

### 3. API/Specifications
- Exported hooks, components, functions
- Props interfaces
- Types and interfaces
- Backend endpoints (if applicable)

### 4. Usage Examples
- How to use the feature
- Code examples showing common patterns

### 5. Dependencies
- External dependencies
- Internal dependencies (other features)

## Keeping Docs in Sync

**When updating code:**
- Review README.md to see if it needs updates
- Update README when adding new functionality, changing APIs, or modifying structure
- Update examples if usage patterns change

**When updating README:**
- Review the actual code to ensure README accurately reflects implementation
- If README describes functionality that doesn't exist, suggest implementing it
- If README describes outdated patterns, update the code to match

**Best Practice:**
- Update README.md as part of the same PR/commit that changes the feature code
- Treat README updates as part of the feature work, not separate documentation work

## Example

```markdown
# Auth Feature

## Overview

Handles user authentication: sign up, sign in, and sign out. Manages user sessions and provides authentication state to the application.

## Structure

```
auth/
├── AuthProvider.tsx    # Context provider, exposes useAuth hook, types, etc
├── SignInForm.tsx      # Sign in form component
├── SignUpForm.tsx      # Sign up form component
├── ...
└── README.md           # This file
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

```tsx
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
```

## Dependencies

- `react-router` - For protected routes
- Backend: `/api/auth/signup`, `/api/auth/signin`, `/api/auth/signout`
```
