# Design System Skills

> A collection of lessons learned, best practices, and anti-patterns from building and maintaining design systems.

## About

This repository documents real-world experiences and insights from design system development. Each "skill" is a lesson learned from actual projects, with concrete code examples showing what works and what doesn't.

## Skills Index

### Component API Design

- [**Eliminating Repeated className Patterns**](./skills/component-refactoring/repeated-classnames.md) - When the same className structure appears 3+ times, refactor it into a props-based API

### Coming Soon

- Backward Compatibility Strategies
- Props API vs Sub-Components: When to Use Each
- Component Composition Patterns
- Design Token Management
- Type-Safe Component Libraries
- Accessibility-First Development

## Philosophy

These skills are based on a simple principle:

> **If you're doing the same thing 3+ times, it's time to abstract it.**

Good design systems reduce boilerplate, enforce consistency, and make developers' lives easier. Each skill in this repository shows how to identify repetitive patterns and refactor them into better abstractions.

## Skill Format

Each skill follows this structure:

- **The Principle**: One-sentence summary
- **Case Study**: Real example with before/after code
- **Implementation**: Step-by-step refactoring guide
- **Design Decisions**: Why we chose this approach
- **Key Takeaway**: Memorable principle to apply

## Contributing

Have your own design system lessons to share? Contributions are welcome!

1. Fork this repository
2. Add your skill in the appropriate category
3. Follow the skill template (see [TEMPLATE.md](./TEMPLATE.md))
4. Submit a pull request

## About

Building design systems for modern web applications. Learning in public, sharing what works (and what doesn't).

- **GitHub**: [@EllaLiu0401](https://github.com/EllaLiu0401)

## License

MIT - Feel free to use these insights in your own projects.

---

**Start here**: [Eliminating Repeated className Patterns](./skills/component-refactoring/repeated-classnames.md) - Learn how to identify and refactor repeated className patterns into cleaner props-based APIs.
