# State Placement

Start local with useState; expand cautiously.

## Why

Global state adds indirection and coupling. Keep state where it's used. If multiple components need the same data, lift it only as high as necessary—not to the root.

## Guidance

- Start with `useState` inside the component.
- Lift to a shared parent when siblings need the same state.
- Move state down the component tree to avoid unnecessary re-renders
- Use context/provider only for the region that needs it; don't wrap the whole app.
- Prefer deriving state over hooks (useState/useEffect)

## Example

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

```tsx
// app/features/products/ProductList.tsx
// ✅ Good: Lift to shared parent when siblings need same state
function ProductList({ products }: { products: Product[] }) {
  const [selectedId, setSelectedId] = useState<string | null>(null);
  return (
    <>
      <ProductFilters selectedId={selectedId} />
      {products.map(p => (
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
    <UserPreferencesProvider> {/* Scoped to dashboard region */}
      <DashboardContent />
    </UserPreferencesProvider>
  );
}
```

```tsx
// app/features/cart/CartSummary.tsx
// ✅ Good: Prefer deriving state over hooks
function CartSummary({ items }: { items: CartItem[] }) {
  // Derived state - no useState/useEffect needed
  const total = items.reduce((sum, item) => sum + item.price * item.quantity, 0);
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
