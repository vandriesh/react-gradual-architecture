# Frameworks

Every React app sits on a foundation (Next.js, React Router, TanStack Start, etc.).
Follow the framework's official recommendations for structure and best practices.
Keep framework glue out of the domain code so the app can move across frameworks.


## Rules

- Framework conventions win; keep the framework layer standard and doc-aligned.
- Avoid framework-only types leaking into domain models or business logic.
- Write business logic that works with any UI platform.
- Aim for isolation at both levels when possible: framework and platform agnostic;
- Write business logic as pure TypeScript with zero framework imports.
- If code can be written without framework, do it.


## General structure

- Framework wiring stays in framework layer; domain code lives separately
- Framework wiring is thin; most code should live under <app>/features dir

## Success Criteria

- Swapping frameworks does not force changes in domain logic.
- Domain code is testable without framework/platform mocks/api.
