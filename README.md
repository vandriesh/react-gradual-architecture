# React Gradual Architecture

> Incremental React code organization guidelines for AI-assisted development

An [agent skill](https://agentskills.io) that helps you organize React code without over-structuring. The core philosophy: **start small** (one file), extract only when it improves clarity or reuse.

## Overview

This skill provides 12 practical rules across 5 categories to help you:

- Create new React features without premature structure
- Organize component files and folders incrementally
- Refactor growing components at the right time
- Decide when to extract hooks, UI, or utils
- Structure feature directories and shared code

**Key principle:** Apply the smallest change that restores clarity. Extract only when it improves readability or enables reuse.

## Installation

Install this skill in your AI coding environment:

```bash
npx add-skill vandriesh/react-gradual-architecture
```

Or manually add to your `.cursor/skills/` or equivalent directory.

## Quick Start

When working with AI coding assistants, use prompts like:

```
Create a new React authentication feature following @react-gradual-architecture principles
```

```
Refactor this component using @react-gradual-architecture guidelines
```

The skill will guide the AI to:
1. Start in a single file
2. Extract only when scanning becomes difficult
3. Organize by feature, not by type
4. Keep state local, lift only when needed

## Rules Summary

### Foundation (CRITICAL)
- **Framework conventions** - Keep domain code framework-agnostic

### Starting (CRITICAL)
- **Start small** - Begin features in a single file
- **Component composition** - Prefer composition over configuration

### Organization & Structure (HIGH)
- **Features folder** - Place features under `src/features/<feature>/`
- **Locality** - Keep related files close to the feature
- **Reuse boundaries** - Wait for the second use before extracting
- **Feature README** - Document structure and specifications

### Extraction (MEDIUM)
- **Extract when scrolling** - If scanning is difficult, extract
- **Hooks vs functions** - Hooks for React logic, plain functions for everything else
- **UI/logic/utils** - Extract when concerns diverge
- **Container/presentational** - Separate data from UI when needed

### Data & State (MEDIUM)
- **Types placement** - Define types where data is born
- **State placement** - Keep state local, lift only as high as needed

## Documentation

- [`SKILL.md`](SKILL.md) - Quick reference guide for AI agents
- [`AGENTS.md`](AGENTS.md) - Comprehensive guide with detailed examples (1000+ lines)
- [`rules/`](rules/) - Individual rule files with edge cases
- [`examples/PROMPTS.md`](examples/PROMPTS.md) - Example prompts for common tasks

## Compatibility

- React
- Works with Cursor, GitHub Copilot, and other AI coding assistants

## Examples

### Creating a new feature

```
Create a user profile feature following @react-gradual-architecture:
- Start in a single file
- Add display and edit functionality
- Handle data fetching
```

The AI will create a single `UserProfile.tsx` file and only extract when the file grows beyond ~100 lines or concerns diverge.

### Refactoring existing code

```
Refactor this 300-line component using @react-gradual-architecture principles:
- Extract when scanning (comprehending) becomes difficult
- Separate UI from data logic
- Keep types where data is born
```

See [`examples/PROMPTS.md`](examples/PROMPTS.md) for more examples.

## Philosophy

This skill embraces **gradual architecture** - the idea that structure should emerge from needs, not be imposed upfront:

1. **Start simple** - One file is enough to begin
2. **Extract when needed** - Let pain points guide extraction
3. **Organize by domain** - Feature folders, not technical folders
4. **Keep things close** - Locality reduces cognitive load
5. **Wait for patterns** - Second use reveals the right abstraction

## Contributing

Contributions welcome! Please feel free to submit issues or pull requests.

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Author

Created by [@vandriesh](https://github.com/vandriesh)

## Resources

- [Agent Skills Specification](https://agentskills.io/specification)
- [Vercel Agent Skills](https://github.com/vercel-labs/agent-skills) - Inspired by Vercel's collection of agent skills

