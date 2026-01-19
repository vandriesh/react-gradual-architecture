# Container and Presentational

Container wires data and callbacks; presentational renders UI from props.

## Why

Separating orchestration from display keeps components reusable and testable. Presentational components have no state and no domain knowledge. Only UI state and ui logic (e.g. active tab). Use this extraction when it improves reuse or testability; avoid it for simple components.

## When to extract

- When you need to reuse the UI with different data sources
- When you want to test UI separately from data logic
- When the component grows and mixing concerns reduces clarity

## Example

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
```

```tsx
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
