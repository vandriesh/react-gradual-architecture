# Prefer Locality

Keep related files close to the feature they serve. Avoid global folders until reuse forces it.

## Why

Locality reduces mental jumps. You can understand a feature by opening its nearby files.

## Example

```tsx
// Before: shared folder without real sharing
// /components
// /hooks
// /utils
```

```tsx
// After: feature-local files
// app/features/orders/OrdersView.tsx
// app/features/orders/useOrders.ts
// app/features/orders/orders-utils.ts
```
