# Extract UI, Logic, and Utils

When responsibilities diverge, extract UI/presentational pieces from logic and utils.

## Why

UI reads better when it is not buried in data fetching or heavy transformation logic. Utilities are easier to test and reuse when they are separate.

**Generic UI vs Domain UI:**

- **Generic UI** (free of domain/logic): Can be grouped/organized into a ui-lib and swapped with another library if it follows common generic naming patterns (e.g., `Button`, `Input`, `Card`). These are reusable, framework-agnostic components.

- **Domain UI**: Cannot be swapped—it's specific to your business domain. Domain UI components are wrappers/enhancers around generic UI that add domain-specific behavior, styling, or logic. Be aware of this distinction when organizing UI components.

## Example

```tsx
// app/features/account/AccountSummary.tsx
// Before: UI and data logic tightly coupled
export function AccountSummary({ userId }: { userId: string }) {
  const [data, setData] = useState<Account | null>(null)
  useEffect(() => {
    fetch(`/api/accounts/${userId}`)
      .then((res) => res.json())
      .then(setData)
  }, [userId])
  return <SummaryCard account={data} />
}
```

```tsx
// After: UI uses extracted hook
// app/features/account/AccountSummary.tsx
export function AccountSummary({ userId }: { userId: string }) {
  const account = useAccount(userId);
  return <SummaryCard account={account} />;
}

// app/features/account/useAccount.ts
function fetchAccount(userId: string) {
  return fetch(`/api/accounts/${userId}`).then((res) => res.json());
}

function useAccount(userId: string) {
  const [data, setData] = useState<Account | null>(null);
  useEffect(() => {
    fetchAccount(userId).then(setData);
  }, [userId]);
  return data;
}
```
