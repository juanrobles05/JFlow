# JFlow Architecture

This document describes the overall architecture, design patterns, and structure of the JFlow CLI tool.

---

## Overview

JFlow is a Node.js-based CLI application designed to standardize Git commits through an interactive and configurable workflow. The architecture follows a modular design to ensure extensibility, maintainability, and ease of testing.

---

## Architecture Principles

1. **Modularity**: Each feature/command is isolated in its own module
2. **Configuration-driven**: Behavior is controlled through `.jflowrc` configuration
3. **Extensibility**: Plugin-ready architecture for future ecosystem growth
4. **Validation-first**: All inputs are validated before execution
5. **User-friendly**: Interactive prompts with clear feedback and error messages

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                         JFlow CLI                            │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      Command Router                          │
│              (Commander.js / CLI Framework)                  │
└─────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
┌──────────────┐    ┌──────────────┐      ┌──────────────┐
│   Commands   │    │    Config    │      │  Validators  │
│              │    │   Manager    │      │              │
│ - commit     │    │              │      │ - Rules      │
│ - init       │◄───┤ - Load       │◄─────┤ - Schema     │
│ - config     │    │ - Update     │      │ - Messages   │
│ - types      │    │ - Validate   │      │              │
│ - check      │    │              │      │              │
│ - fmt        │    │              │      │              │
└──────────────┘    └──────────────┘      └──────────────┘
        │                   │                      │
        ▼                   ▼                      ▼
┌─────────────────────────────────────────────────────────────┐
│                         Core Modules                         │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Prompter    │  │  Formatter   │  │  Git Utils   │      │
│  │ (Inquirer)   │  │              │  │              │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      Git Integration                         │
│                    (git commit -m "...")                     │
└─────────────────────────────────────────────────────────────┘
```

---

## Directory Structure

```
jflow/
├── bin/
│   └── jflow.js                 # CLI entry point
├── src/
│   ├── commands/                # Command implementations
│   │   ├── commit.js           # Interactive commit flow
│   │   ├── init.js             # Initialize .jflowrc
│   │   ├── config.js           # Modify configuration
│   │   ├── types.js            # Display commit types
│   │   ├── check.js            # Validate commit message
│   │   └── fmt.js              # Format commit message
│   ├── core/                   # Core functionality
│   │   ├── prompter.js         # Interactive prompts
│   │   ├── formatter.js        # Message formatting
│   │   ├── validator.js        # Validation logic
│   │   └── git.js              # Git operations
│   ├── config/                 # Configuration management
│   │   ├── loader.js           # Load .jflowrc
│   │   ├── schema.js           # Config schema validation
│   │   └── defaults.js         # Default configuration
│   ├── utils/                  # Utility functions
│   │   ├── logger.js           # Console output
│   │   ├── errors.js           # Error handling
│   │   └── helpers.js          # Common helpers
│   └── index.js                # Main CLI setup
├── templates/                  # Configuration templates
│   └── .jflowrc.template       # Default .jflowrc template
├── tests/                      # Test suite
│   ├── unit/
│   ├── integration/
│   └── fixtures/
├── docs/                       # Additional documentation
├── package.json
├── README.md
├── ARCHITECTURE.md
├── CONTRIBUTING.md
└── LICENSE
```

---

## Core Components

### 1. Command Router

**Location**: `src/index.js`

**Responsibility**:

- Parse command-line arguments
- Route to appropriate command handlers
- Handle global flags and options
- Display help and version information

**Technology**: Commander.js (or similar CLI framework)

---

### 2. Commands Module

**Location**: `src/commands/`

Each command is a self-contained module that:

- Implements specific CLI functionality
- Uses core modules for shared operations
- Validates inputs before execution
- Provides clear user feedback

#### Command: `commit`

- Loads configuration from `.jflowrc`
- Prompts user for commit information (type, scope, description, body, footer)
- Validates inputs according to rules
- Formats the commit message
- Executes `git commit` with the generated message

#### Command: `init`

- Checks if `.jflowrc` already exists
- Prompts for initial configuration preferences
- Creates `.jflowrc` file with selected options
- Provides confirmation and next steps

#### Command: `config`

- Displays current configuration
- Allows interactive modification of settings
- Validates new values
- Updates `.jflowrc` file

#### Command: `types`

- Loads configured commit types
- Displays formatted table with descriptions
- Shows examples for each type

#### Command: `check`

- Accepts commit message as input
- Validates against configured rules
- Reports validation results with detailed feedback
- Returns appropriate exit code

#### Command: `fmt`

- Accepts raw commit message
- Formats according to conventions
- Outputs formatted message
- Can be piped to other tools

---

### 3. Configuration Manager

**Location**: `src/config/`

**Responsibilities**:

- Load `.jflowrc` from project root
- Merge with default configuration
- Validate configuration schema
- Provide configuration to other modules

**Configuration Schema**:

```json
{
  "language": "en",                    // UI language (en, es, etc.)
  "allowEmptyScope": false,            // Allow commits without scope
  "types": [...],                      // Allowed commit types
  "scopes": [...],                     // Predefined scopes (optional)
  "convention": "jflow",               // Convention style
  "rules": {                           // Validation rules
    "maxHeaderLength": 72,
    "minDescriptionLength": 3,
    "requireIssueRef": false,
    "allowedFooters": ["Refs", "Closes", "Breaking"]
  },
  "prompts": {                         // Customize prompts
    "skipBody": false,
    "skipFooter": false
  }
}
```

---

### 4. Validator

**Location**: `src/core/validator.js`

**Responsibilities**:

- Validate commit message structure
- Enforce configured rules
- Check type, scope, and format
- Validate character limits
- Verify required fields

**Validation Flow**:

```
Input Message
     │
     ▼
Parse Structure
     │
     ├──► Validate Type (must be in allowed types)
     │
     ├──► Validate Scope (required/optional based on config)
     │
     ├──► Validate Description (length, format)
     │
     ├──► Validate Body (if present)
     │
     └──► Validate Footer (format, allowed types)
     │
     ▼
Return Validation Result
```

---

### 5. Formatter

**Location**: `src/core/formatter.js`

**Responsibilities**:

- Format commit message according to convention
- Apply consistent styling
- Handle multi-line bodies
- Format footers correctly

**Output Format**:

```
<type>(<scope>): <description>

<body>

<footer>
```

Example:

```
feat(cli): add interactive commit command

Implemented the main commit command with interactive prompts
for type, scope, description, body, and footer.

Refs: #123
```

---

### 6. Prompter

**Location**: `src/core/prompter.js`

**Responsibilities**:

- Display interactive prompts
- Collect user input
- Provide suggestions and autocomplete
- Handle multi-language support

**Technology**: Inquirer.js (or similar prompting library)

**Prompt Flow for `jflow commit`**:

1. Select commit type (list)
2. Enter scope (input with autocomplete)
3. Enter description (input with validation)
4. Enter body (optional, multiline editor)
5. Enter footer (optional, input)
6. Confirm and commit

---

### 7. Git Integration

**Location**: `src/core/git.js`

**Responsibilities**:

- Check if in a Git repository
- Execute Git commands
- Handle Git errors
- Validate Git state (staged files, etc.)

**Functions**:

- `isGitRepo()` - Check if current directory is a Git repository
- `hasChanges()` - Check if there are staged changes
- `commit(message)` - Execute git commit with message
- `getLastCommit()` - Get last commit message (for check/fmt)

---

## Data Flow

### Commit Command Flow

```
User runs: jflow commit
         │
         ▼
Load .jflowrc configuration
         │
         ▼
Validate Git repository
         │
         ▼
Check for staged changes
         │
         ▼
Prompt for commit type ──────► Validate against allowed types
         │
         ▼
Prompt for scope ────────────► Validate format and requirements
         │
         ▼
Prompt for description ──────► Validate length and format
         │
         ▼
Prompt for body (optional)
         │
         ▼
Prompt for footer (optional) ► Validate footer format
         │
         ▼
Format complete message
         │
         ▼
Display preview
         │
         ▼
Confirm with user
         │
         ▼
Execute git commit
         │
         ▼
Display success message
```

---

## Extension Points

JFlow is designed to be extensible for future ecosystem growth:

### 1. Plugin System (Future)

- Custom validators
- Custom formatters
- Custom prompt types
- Integration hooks

### 2. Convention Adapters (Future)

- Conventional Commits
- Angular convention
- Custom conventions

### 3. Integrations (Future)

- Issue tracker linking (Jira, GitHub Issues)
- CI/CD integration
- Changelog generation
- Release automation

---

## Error Handling

All modules implement consistent error handling:

1. **Validation Errors**: Clear messages about what's wrong and how to fix it
2. **Configuration Errors**: Detailed feedback on config issues
3. **Git Errors**: Handle missing repo, no changes, etc.
4. **System Errors**: Graceful handling of file system issues

**Error Flow**:

```
Error occurs
     │
     ▼
Catch in module
     │
     ▼
Transform to user-friendly message
     │
     ▼
Log with appropriate level (error/warning/info)
     │
     ▼
Exit with appropriate code (0 = success, >0 = error)
```

---

## Testing Strategy

### Unit Tests

- Individual functions in core modules
- Validators with various inputs
- Formatters with edge cases
- Configuration loader

### Integration Tests

- Full command flows
- Git integration
- Configuration loading and validation

### E2E Tests

- CLI command execution
- User interaction simulation
- Real Git operations in test repo

---

## Security Considerations

1. **Input Validation**: All user inputs are validated before use
2. **Command Injection**: Git commands are properly escaped
3. **File System**: `.jflowrc` is validated before parsing
4. **Dependencies**: Regular security audits of npm packages

---

## Performance Considerations

1. **Lazy Loading**: Commands load only when needed
2. **Caching**: Configuration is cached during execution
3. **Async Operations**: Git commands run asynchronously
4. **Minimal Dependencies**: Keep package size small

---

## Future Architecture Enhancements

1. **Plugin Architecture**: Allow third-party extensions
2. **API Mode**: Programmatic usage beyond CLI
3. **Web UI**: Optional web interface for commit creation
4. **Team Presets**: Shareable configuration presets
5. **Analytics**: Optional usage analytics for improvement

---

## Technology Decisions

### Why Node.js?

- Cross-platform compatibility
- Rich CLI ecosystem (Commander, Inquirer)
- Easy to distribute via npm
- Familiar to JavaScript developers

### Why Configuration File?

- Portable across team members
- Version-controlled with project
- Easy to customize per project
- Overridable at multiple levels

### Why Interactive Prompts?

- Guides users through process
- Reduces errors
- Provides immediate validation
- Better UX than memorizing syntax

---

## References

- [Conventional Commits](https://www.conventionalcommits.org/)
- [Semantic Versioning](https://semver.org/)
- [Commander.js Documentation](https://github.com/tj/commander.js)
- [Inquirer.js Documentation](https://github.com/SBoudrias/Inquirer.js)

---

## Changelog

| Version | Date       | Changes                       |
| ------- | ---------- | ----------------------------- |
| 0.1.0   | 2025-11-19 | Initial architecture document |

---

**Maintained by**: Juan Diego Robles de la Ossa
