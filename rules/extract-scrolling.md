# Extract When Scanning Hurts

If you are scrolling to understand one concern, it is time to extract that concern.

## Why

When a file exceeds one screen height and requires frequent scrolling, the cognitive load rises. Extract the chunk that makes the page feel crowded.

**Note:** On a typical (27" 2k) vertical monitor (~95 LOC visible without scrolling), files exceeding this height often benefit from extraction.


## Order
 - Consider the biggest chunk that can be extracted first - logic/react hook.
 - Next consider markup + hooks that can be extracted as a presentation component.
 - Utils functions.

## Example

### Before Extraction (240 lines)

```tsx
// app/features/profile/UserProfile.tsx
// Before
// ... (imports L1-L14) ...

15  function UserProfile() {
 16    const [user, setUser] = useState<User | null>(null);
 17    const [loading, setLoading] = useState(true);
 18    // ... more state declarations ...
140    useEffect(() => {
141      const fetchUser = async () => {
142        // ... fetch logic ...
153      };
154      fetchUser();
155    }, [userId]);
156    // ... more hooks and handlers ...
240    return (
241      <div className="user-profile">
242        <div className="header">
243          {/* ... header content ... */}
248        </div>
249        <div className="tabs">
250          {/* ... tab navigation ... */}
268        </div>
269        <div className="content">
270          {/* ... conditional content sections ... */}
300        </div>
301      </div>
302    );
303  }
```

### After Extraction (112 lines)

```tsx
// app/features/profile/UserProfile.tsx
// After
// ... (imports L1-L14) ...

15  function UserProfile() {
 16    const [user, setUser] = useState<User | null>(null);
 17    const [loading, setLoading] = useState(true);
 18    // ... more state declarations ...
 50    useEffect(() => {
 51      const fetchUser = async () => {
 52        // ... fetch logic ...
 63      };
 64      fetchUser();
 65    }, [userId]);
 66    // ... more hooks and handlers ...
112    return (
113      <UserProfileView 
114        user={user}
115        loading={loading}
116        // ... other props ...
121      />
122    );
123  }
```

**Result**: File reduced from 240 lines to 112 lines. The long markup section (128 lines) was extracted into `UserProfileView` component, making the file easier to scan and understand.
