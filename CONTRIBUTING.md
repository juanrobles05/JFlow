# Contributing to JFlow

This document provides guidelines and standards for developing JFlow. While this is primarily a solo project, these guidelines ensure consistency and quality throughout the codebase.

---

## Table of Contents

- [Development Setup](#development-setup)
- [Project Structure](#project-structure)
- [Commit Convention](#commit-convention)
- [Coding Standards](#coding-standards)
- [Testing Guidelines](#testing-guidelines)
- [Documentation](#documentation)

---

## Development Setup

### Prerequisites

- **Node.js**: v16.x or higher
- **npm**: v8.x or higher
- **Git**: v2.x or higher

### Setup

1. **Clone the repository**

   ```bash
   git clone https://github.com/juanrobles05/JFlow.git
   cd JFlow
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

### Install in Development Mode

To test JFlow CLI locally:

```bash
npm link
```

This creates a global symlink to your local development version. Now you can run `jflow` commands from anywhere.

### Unlink After Development

```bash
npm unlink -g jflow
```

### Run in Development

```bash
node bin/jflow.js commit
# or
npm run dev commit
```

---

## Project Structure

```
jflow/
├── bin/
│   └── jflow.js              # CLI entry point
├── src/
│   ├── commands/             # Command implementations
│   ├── core/                 # Core functionality
│   ├── config/               # Configuration management
│   └── utils/                # Utility functions
├── tests/                    # Test files
│   ├── unit/                 # Unit tests
│   ├── integration/          # Integration tests
│   └── fixtures/             # Test fixtures
├── templates/                # Configuration templates
├── docs/                     # Additional documentation
├── .jflowrc                  # Project's own config
├── package.json
├── README.md
├── ARCHITECTURE.md
├── CONTRIBUTING.md
└── LICENSE
```

For detailed architecture information, see [ARCHITECTURE.md](./ARCHITECTURE.md).

---

## Commit Convention

JFlow follows its own commit convention (eating our own dog food!).

### Commit Structure

A valid commit must follow this structure:

```
<type>(<scope>): <short description>

<body>

<footer>
```

---

### 1. TYPE (required)

Must be one of the allowed types in JFlow:

| Type       | Description                                |
| ---------- | ------------------------------------------ |
| `feat`     | New feature                                |
| `fix`      | Bug fix                                    |
| `refactor` | Internal changes without altering behavior |
| `docs`     | Documentation                              |
| `style`    | Code style (formatting, spaces, etc.)      |
| `perf`     | Performance improvements                   |
| `test`     | Tests                                      |
| `chore`    | General maintenance                        |
| `ci`       | CI/CD changes                              |
| `build`    | Build or dependency changes                |
| `revert`   | Revert a commit                            |

> **Note:** JFlow is opinionated. The types listed here correspond to JFlow's internal configuration file. If you change the configuration, this table may vary.

**Examples:**

```
feat
fix
ci
chore
```

---

### 2. SCOPE (required)

Specific area where the change applies.

**Defined scopes for JFlow:**

- `commit` - Commit command and builder
- `config` - Configuration management
- `core` - Core functionality
- `i18n` - Internationalization
- `git` - Git operations
- `cli` - CLI interface
- `release` - Release-related changes
- `hooks` - Git hooks integration

**Example:**

```
feat(commit): ...
```

---

### 3. SHORT DESCRIPTION (required)

- Single line
- Descriptive
- Imperative mode
- Maximum recommended: 72 characters

**Examples:**

```
add interactive commit builder
fix scope validation when config is missing
improve performance of git service
```

---

### 4. BODY (optional but recommended)

Explains:

- What was done
- Why it was done
- Technical details
- Bullet points allowed

**Example:**

```
This update adds the first version of the interactive commit builder.
It guides the user through type, scope and description selection.

- added commit prompts
- added short description validator
- updated configuration loader
```

---

### 5. FOOTER (optional)

Used to relate issues or special notes.

**Allowed formats:**

```
Refs #ID
Closes #ID
BREAKING CHANGE: <description>
Co-authored-by: Name <email>
```

**Examples:**

```
Refs #12
Closes #7
BREAKING CHANGE: Updated commit format to require scope.
```

---

### Complete Examples

#### Example 1: Feature with issue reference

```
feat(commit): add interactive commit builder

This introduces the first iteration of the interactive commit builder,
which asks the user for type, scope and description and generates a
valid commit message accordingly.

- added prompts for type, scope and description
- added basic validation rules
- updated formatter service

Closes #14
```

#### Example 2: Refactor with breaking change

```
refactor(core): rewrite config loader

The config loader has been restructured to allow async validation
and scoped configuration overrides.

BREAKING CHANGE: .jflowrc keys "types" and "scopes" must now be arrays.

Refs #22
```

#### Example 3: Simple fix

```
fix(validator): handle empty scope correctly

Fixed bug where empty scopes were not validated
according to allowEmptyScope configuration.

Closes #45
```

---

### Bad Commit Examples

Avoid commits like these:

```
update stuff
```

```
Fixed bug
```

```
WIP
```

### Using JFlow to Create Commits

Once the `jflow commit` command is implemented, use it for all commits:

```bash
git add .
jflow commit
```

---

## Branching Model

JFlow follows a simplified Git Flow approach:

- `main` - Stable version of the CLI, production-ready
- `develop` - Main development branch, integration point
- `feature/*` - Development of new features (e.g., `feature/interactive-prompts`)
- `fix/*` - Bug fixes (e.g., `fix/scope-validation`)

**Workflow:**

1. Create a branch from `develop`:

   ```bash
   git checkout develop
   git pull
   git checkout -b feature/my-feature
   ```

2. Make your changes and commit following the convention

3. Push your branch:

   ```bash
   git push origin feature/my-feature
   ```

4. Create a Pull Request to `develop`

---

## Pull Requests

Even though this is a solo project, every change should go to `main` through a Pull Request to maintain a clean history and allow for self-review.

**Guidelines:**

- Create PRs from `feature/*` or `fix/*` branches to `develop`
- Use descriptive titles following commit convention format
- Include a brief description of changes
- Review your own code before merging
- Delete branch after merging

**PR Title Examples:**

```
feat(commit): add interactive commit builder
fix(validator): correct scope validation logic
docs(readme): update installation instructions
```

---

## Coding Standards

### JavaScript Style Guide

We follow modern JavaScript best practices:

#### General Rules

- **Use ES6+ features**: arrow functions, destructuring, template literals
- **Prefer `const`** over `let`, avoid `var`
- **Use async/await** over promises when possible
- **Semicolons**: Required
- **Quotes**: Single quotes for strings
- **Indentation**: 2 spaces
- **Line length**: Maximum 100 characters

#### Example Code Style

```javascript
// Good
const getUserInput = async (prompt) => {
  const { answer } = await inquirer.prompt([
    {
      type: "input",
      name: "answer",
      message: prompt,
      validate: (input) => input.length > 0 || "Input cannot be empty",
    },
  ]);
  return answer;
};

// Bad
var getUserInput = function (prompt) {
  return inquirer
    .prompt([
      {
        type: "input",
        name: "answer",
        message: prompt,
      },
    ])
    .then(function (result) {
      return result.answer;
    });
};
```

#### File Naming

- Use **kebab-case** for file names: `commit-formatter.js`
- Use **PascalCase** for classes: `class CommitValidator {}`
- Use **camelCase** for variables and functions: `const validateMessage = () => {}`

#### Comments

```javascript
/**
 * Validates a commit message against configured rules
 * @param {string} message - The commit message to validate
 * @param {Object} config - Configuration object
 * @returns {Object} Validation result with isValid and errors
 */
const validateCommit = (message, config) => {
  // Implementation
};
```

### Error Handling

Always provide meaningful error messages:

```javascript
// Good
if (!fs.existsSync(".jflowrc")) {
  throw new Error(
    'Configuration file not found. Run "jflow init" to create one.',
  );
}

// Bad
if (!fs.existsSync(".jflowrc")) {
  throw new Error("File not found");
}
```

### Module Structure

Each module should follow this pattern:

```javascript
"use strict";

// Imports
const fs = require("fs");
const path = require("path");

// Constants
const DEFAULT_CONFIG_PATH = ".jflowrc";

// Private functions
const _helperFunction = () => {
  // Implementation
};

// Public API
const publicFunction = () => {
  // Implementation
};

// Exports
module.exports = {
  publicFunction,
};
```

---

## Testing Guidelines

### Test Structure

- **Unit tests**: Test individual functions in isolation
- **Integration tests**: Test interactions between modules
- **E2E tests**: Test full CLI commands

### Writing Tests

Use Jest (or similar) for testing:

```javascript
// tests/unit/validator.test.js
const { validateCommit } = require("../../src/core/validator");

describe("Commit Validator", () => {
  describe("validateCommit", () => {
    test("should accept valid commit message", () => {
      const message = "feat(cli): add new feature";
      const config = { types: ["feat"], allowEmptyScope: false };

      const result = validateCommit(message, config);

      expect(result.isValid).toBe(true);
      expect(result.errors).toHaveLength(0);
    });

    test("should reject invalid commit type", () => {
      const message = "invalid(cli): add new feature";
      const config = { types: ["feat"], allowEmptyScope: false };

      const result = validateCommit(message, config);

      expect(result.isValid).toBe(false);
      expect(result.errors).toContain("Invalid commit type");
    });
  });
});
```

### Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage
npm run test:coverage

# Run specific test file
npm test -- validator.test.js
```

### Test Coverage

Maintain minimum 80% code coverage. Check coverage with:

```bash
npm run test:coverage
```

---

## Documentation

### When to Update Documentation

- Adding new features
- Changing existing behavior
- Fixing bugs that affect usage
- Adding new configuration options

### Documentation Files

- `README.md` - Overview and quick start
- `ARCHITECTURE.md` - Technical architecture
- `CONTRIBUTING.md` - This file
- Code comments - Complex logic explanation
- JSDoc - Function documentation

### Documentation Style

- Use clear, concise language
- Include code examples
- Provide context for why, not just how
- Keep it up to date

---

## Release Process

1. Update version in `package.json` (following SemVer)
2. Update `CHANGELOG.md`
3. Create release commit: `chore(release): v1.0.0`
4. Create Git tag: `git tag v1.0.0`
5. Push: `git push && git push --tags`
6. Publish to npm: `npm publish`
7. Create GitHub release with changelog

---

## License

This project is licensed under the MIT License.

---

Maintained by Juan Diego Robles de la Ossa
