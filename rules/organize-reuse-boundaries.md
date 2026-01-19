# Reuse Reveals Boundaries

Wait for the second use to decide where shared code should live.

## Why

The first use doesn't reveal the correct scope. The second shows whether the code is
feature-local, domain-shared, or app-wide. Extracting too early creates a grab-bag
`common/` and makes code harder to navigate.

## Guidance

- If both uses are in the same feature, keep it local.
- If uses span two related features, extract to a domain/shared folder.
- Only move to a root shared area when it's reused broadly.

## Example

```tsx
// Before: premature shared util for a single use
export function formatPrice(value: number) {
  return `$${value.toFixed(2)}`;
}
```

```tsx
// After: extract when the second use appears
// app/features/cart/CartTotal.tsx
function CartTotal({ total }: { total: number }) {
  return <span>{formatPrice(total)}</span>;
}

// app/features/invoices/InvoiceTotal.tsx
function InvoiceTotal({ total }: { total: number }) {
  return <strong>{formatPrice(total)}</strong>;
}

// app/features/shared/formatPrice.ts - features/shared is closest common ground
export function formatPrice(value: number) {
  return `$${value.toFixed(2)}`;
}
```
