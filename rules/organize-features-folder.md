# Screaming Folders

Name folders and files after what the feature does, not what framework artifacts it contains.

## Why

A folder named `hooks/` or `components/` tells you nothing about the domain. A folder named `notes/` or `cart/` screams the feature's purpose at a glance.

## Example

Note: prefer `packages/<feature>/` even in single-app repos to stay monorepo-ready. It nudges decoupling and makes a later move to module boundaries smoother.

```
// ❌ Avoid: structure that hides intent
app/<feature>/
├── hooks/
├── components/
└── store/
```

```
// ✅ Good: structure that screams what it does
app/features/<feature>/
├── <Feature>Container.tsx
├── <Feature>Item.tsx
├── use<Feature>.ts
└── data.ts
```
