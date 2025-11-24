# JFlow

**Standardize and improve your commits with a flexible and configurable CLI**

> Your assistant for creating clean, consistent, and professional commits.

---

## What is JFlow?

**JFlow is a CLI tool built with Node.js that facilitates, automates, and standardizes commits within your projects**, allowing you to maintain best practices, consistency, and traceability.

It offers custom configurations, support for commit styles, rules, validations, interactive prompts, future extensions, and an ecosystem designed to help you commit better.

---

## Project Goal

Create a **complete ecosystem** for development workflows that:

- Guides developers to write clear and quality commits
- Enforces optionally configurable best practices
- Allows customization of types, scopes, language, and rules
- Generates consistent commits based on well-known conventions
- Serves as a foundation for plugins and future tools in the JFlow ecosystem

---

## Tech Stack

- **Node.js**
- CLI built with:
  - Commander.js / Inquirer (or alternatives of your choice)
  - fs, path, and utils for configuration
- Configuration system based on `.jflowrc` file

---

## CLI Commands (proposed for v1)

These commands are not yet implemented, but are documented here:

### `jflow commit`

Interactive CLI to generate a standardized commit.
Includes: type, mandatory scope, description, optional body, footer, issue linking, and validations.

### `jflow init`

Creates an initial `.jflowrc` file with basic configuration.

### `jflow config`

Allows modifying `.jflowrc` options from the terminal.

### `jflow types`

Displays the table of supported commit types.

### `jflow check`

Validates whether your message complies with the configured rules.

### `jflow fmt`

Formats a given commit to follow established conventions.

---

## Project Conventions

JFlow uses:

- Custom commit convention (with your own types: feat, fix, refactor, docs, style, perf, test, chore, ci, build, revert)
- Mandatory scopes
- **SemVer** versioning
- Extensible and configurable via `.jflowrc`

All extended information, styles, examples, and best practices can be found in the **[CONTRIBUTING.md](./CONTRIBUTING.md)** file.

---

## Installation

```bash
npm install -g jflow
```

---

## Basic Usage

```bash
jflow commit
```

This will launch the interactive CLI with all the necessary steps to generate a correct commit.

---

## .jflowrc File (example)

```json
{
  "language": "en",
  "allowEmptyScope": false,
  "types": [
    "feat",
    "fix",
    "refactor",
    "docs",
    "style",
    "perf",
    "test",
    "chore",
    "ci",
    "build",
    "revert"
  ],
  "convention": "jflow"
}
```

---

## License

This project is licensed under the MIT License. You can view it in the [LICENSE](./LICENSE) file.

---

## Contributing

Read the guidelines in **[CONTRIBUTING.md](./CONTRIBUTING.md)**.

You will find:

- Code conventions
- How to write commits
- Project structure
- Guide for creating new features

---

## Author

**Juan Diego Robles de la Ossa**  
Creator and main maintainer of the JFlow project.
