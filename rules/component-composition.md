# Create Components with Composition

Prefer composition over configuration. Use children and component composition instead of many props. Avoid configuring behavior through props.

## Why

Components with many props become hard to use and maintain. Composition provides flexibility and keeps components focused. Configuration props create coupling and reduce reusability.

**Exception:** Dedicated generic props with the purpose of variants (e.g., `variant="primary"`, `size="large"`) are acceptable as they represent visual/behavioral variations of the same component.

## Example

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
