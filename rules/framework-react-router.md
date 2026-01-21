# React Router / Remix Server Modules

Mark server-only code with `.server` suffix to prevent accidental bundling into client code.

## Why

React Router v7 uses the `.server` file convention to exclude code from client bundles. The build will fail if server code accidentally ends up in the client module graph, preventing exposure of secrets like API keys, database credentials, or authentication tokens.

## File Naming

Add `.server` before the extension for server-only modules:

```
packages/<feature>/
├── src/
│   ├── service.server.ts       # Database operations (server-only)
│   ├── <feature>Container.tsx  # React component (client)
│   └── index.ts                # Public exports
```

## When to Use .server

- Database connections and queries
- Authentication utilities (password hashing, JWT signing)
- Sensitive environment variables
- API integrations with secrets
- Any code that should never run in the browser

## When NOT to Use .server

- Route modules (loaders, actions) - they have special handling
- Shared types and interfaces
- Utility functions that work on both client and server
- React components

## Reference

- [React Router .server modules](https://reactrouter.com/api/framework-conventions/server-modules)
