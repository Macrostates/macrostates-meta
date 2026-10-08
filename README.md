# Specification packages

This package defines the specification package system. It describes how
specification packages are structured, versioned, read, composed, and reused
across projects.

## Macrostates

This package is part of [Macrostates](https://github.com/orgs/Macrostates), a
project for composing reusable specification packages into specs-driven
development projects.

## Scope

- Specification package concepts.
- Package structure and metadata.
- Package versioning and Git release tags.
- Project-level package selection.
- Project composition through root specification files.
- Progressive disclosure for package documents.

## Reading order

1. [Concepts](001_concepts.md)
2. [Project composition](002_project-composition.md)
3. [Progressive disclosure](003_progressive-disclosure.md)

## Conditional annexes

- [Project kickoff](annex_project-kick-off.md): read when preparing a new
  project's specification set from a composition manifest.

## Resources

- [Project kickoff instructions](project-kickoff.md): portable LLM instructions
  to copy into a new project's `specs/` directory and activate through its
  `main.md`. This resource is not part of the package's core reading order.
