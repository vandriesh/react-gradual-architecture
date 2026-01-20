# Example Prompts

This file contains example prompts demonstrating how to use the `react-gradual-architecture` skill with AI coding assistants.

## Creating a New React App with Vite

### Basic Setup

```
Create a new React app using Vite with TypeScript template.
Follow @react-gradual-architecture principles.
```

**Expected behavior:**
- AI creates a clean Vite + React + TypeScript project
- Follows standard Vite conventions (framework-conventions rule)
- Sets up basic project structure without over-engineering

### With Initial Feature

```
Create a new React app using Vite with TypeScript template.
Add a simple todo list feature.
Follow @react-gradual-architecture principles:
- Start in a single file
- Extract only when needed
```

**Expected behavior:**
- Creates Vite project
- Implements todo list in `src/features/todos/Todos.tsx` (single file)
- Only extracts if the file exceeds ~100 lines
- Uses composition for components

## Adding Authentication Feature

### Minimal Auth Feature

```
Add authentication feature to my React app following @react-gradual-architecture:
- Sign up, sign in, sign out
- Protected routes
- User session management
- Start in a single file, extract when scanning becomes difficult
```

**Expected behavior:**
- Creates `src/features/auth/Auth.tsx` (single file to start)
- Implements all auth logic in one place
- Only extracts when file grows beyond ~100 lines or concerns diverge
- Defines types where data is born (auth-api types stay with API functions)
- Keeps state local (useAuth hook for auth state)

### With Specific Requirements

```
Add authentication to my React app with these requirements:
- Email/password sign up and sign in
- JWT token storage
- Automatic token refresh
- Protected route wrapper component

Follow @react-gradual-architecture principles:
- Start small in one file
- Extract when scanning becomes difficult
- Keep domain logic framework-agnostic
- Create feature README
```

**Expected behavior:**
- Starts in `src/features/auth/Auth.tsx`
- Extracts as file grows:
  - `useAuth.ts` - React hook for state management
  - `auth-api.ts` - API functions (framework-agnostic)
  - `ProtectedRoute.tsx` - Route wrapper component
- Creates `src/features/auth/README.md` documenting the feature
- Types defined where data is born (in auth-api.ts)

## Refactoring Existing Components

### General Refactor

```
Refactor this component using @react-gradual-architecture principles:
- Extract when scanning becomes difficult
- Separate UI from data logic
- Keep types where data is born
- Use hooks for React logic, plain functions for everything else

[paste your component code here]
```

**Expected behavior:**
- Analyzes component for extraction opportunities
- Extracts presentation components if file is too long
- Separates data fetching logic into hooks
- Extracts pure utility functions (no React dependencies)
- Keeps types co-located with their usage

### Specific Extraction

```
This UserProfile component is 250 lines. Apply @react-gradual-architecture:
- Extract presentation components first
- Then extract data fetching logic
- Keep state local where possible

[paste component code]
```

**Expected behavior:**
- Extracts `UserProfileView.tsx` for presentation
- Keeps data logic in `UserProfile.tsx` or extracts to `useUserProfile.ts`
- Moves state down where possible to avoid unnecessary re-renders

## Organizing Growing Features

### Feature Organization

```
My cart feature has grown. Help me organize it following @react-gradual-architecture:
- Current: single Cart.tsx file (180 lines)
- Organize files by concern
- Keep locality principle
- Create feature README

[paste Cart.tsx code]
```

**Expected behavior:**
- Creates `src/features/cart/` folder structure:
  ```
  cart/
  ├── Cart.tsx (container)
  ├── CartView.tsx (presentation)
  ├── useCart.ts (React hook)
  ├── cart-utils.ts (pure functions)
  └── README.md (feature documentation)
  ```
- Extracts only what's necessary
- Keeps related files close

### Extracting Shared Code

```
I have the same formatPrice function in Cart and Invoice features.
Apply @react-gradual-architecture reuse-boundaries rule to extract it properly.
```

**Expected behavior:**
- Since it's the second use, extracts to shared location
- If features are related: `src/features/shared/formatPrice.ts`
- If broadly used: `src/shared/formatPrice.ts` or `src/utils/formatPrice.ts`
- Plain function (not a hook, since no React dependencies)

## Data Fetching Patterns

### Adding Data Fetching

```
Add data fetching to this component following @react-gradual-architecture:
- Keep API functions framework-agnostic
- Use hooks for React state management
- Define types where data is born
- Start simple, extract when needed

[paste component]
```

**Expected behavior:**
- Creates/extracts API functions as plain TypeScript (no framework imports)
- Wraps in custom hook for React state (useState, useEffect)
- Defines API response types with API functions
- Component stays clean, just uses the hook

### Optimizing Data Fetching

```
Optimize data fetching in this feature:
- Extract container from presentational components
- Separate data logic from UI
- Keep state placement optimal

[paste feature code]
```

**Expected behavior:**
- Separates container (data orchestration) from presentational (UI) components
- Lifts state only as high as needed
- Uses composition for flexibility

## Component Composition

### Converting Props to Composition

```
This Card component has too many configuration props.
Convert to composition following @react-gradual-architecture principles.

[paste Card component]
```

**Expected behavior:**
- Converts boolean/config props to composition with children
- Creates sub-components (Card.Header, Card.Content, Card.Footer)
- Keeps variant props for visual variations (variant="outlined")
- Improves flexibility and reduces prop bloat

## State Management

### Optimizing State Placement

```
Review state placement in this feature following @react-gradual-architecture:
- Keep state local where possible
- Lift only as high as needed
- Move state down to avoid re-renders
- Use derived state instead of useState where appropriate

[paste feature code]
```

**Expected behavior:**
- Analyzes current state placement
- Suggests moving state down to components that actually use it
- Identifies derived state that doesn't need useState/useEffect
- Recommends scoped context instead of global if needed

## Complete Feature Development

### End-to-End Feature with Incremental Approach

```
Build a complete product catalog feature for my e-commerce app:
- Product listing with filters
- Product detail view
- Add to cart functionality
- Search and sort

Follow @react-gradual-architecture from start to finish:
1. Start in a single file
2. Extract incrementally as the feature grows
3. Organize by feature, not by type
4. Create feature README
5. Keep types where data is born
```

**Expected behavior:**
- Starts with `src/features/products/Products.tsx`
- Builds functionality incrementally
- Extracts when file exceeds ~100 lines or concerns diverge:
  - `ProductList.tsx` (presentation)
  - `ProductDetail.tsx` (presentation)
  - `ProductFilters.tsx` (presentation)
  - `useProducts.ts` (React hook for state)
  - `products-api.ts` (API functions, types)
  - `products-utils.ts` (pure utilities)
- Creates `src/features/products/README.md`
- Keeps all product-related code in the feature folder

## Tips for Writing Effective Prompts

1. **Be specific about the principle**: Reference specific rules (e.g., "follow start-small rule")
2. **Mention extraction triggers**: "extract when scanning becomes difficult" or "file exceeds 100 lines"
3. **Request documentation**: "create feature README" ensures living documentation
4. **Emphasize incremental approach**: "start in one file, extract when needed"
5. **Reference the skill name**: Include "@react-gradual-architecture" in your prompt

## Common Patterns

### Starting New Features
```
Create [feature name] following @react-gradual-architecture:
- Start in a single file
- Extract only when needed
- [specific requirements]
```

### Refactoring Existing Code
```
Refactor this [component/feature] using @react-gradual-architecture:
- Extract when scanning becomes difficult
- [specific extraction goals]
- [paste code]
```

### Organizing Code
```
Organize this [feature] following @react-gradual-architecture:
- Organize by feature, not type
- Keep related files close
- Create feature README
- [paste code]
```
