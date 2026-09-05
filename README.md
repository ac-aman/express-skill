# express-skill

> An open, production-ready AI Agent Skill for building scalable, secure Express.js applications. Compatible with `npx skills`, Claude Code, Cursor, Antigravity, and AI coding assistants.

---

## Overview

`express-skill` provides AI coding assistants with standardized architectural guidelines, production security defaults, and proven middleware patterns when developing Express.js applications. 

By equipping your agent with this skill, it automatically enforces clean separation of concerns, strict input validation, centralized error handling, and security best practices without requiring repeated prompt instructions.

---

## Features & Capabilities

- **Layered Architecture**: Enforces clean 4-layer separation (Routes ➔ Thin Controllers ➔ Domain Services ➔ Repositories/Models).
- **Strict Request Validation**: Uses `zod` schemas to validate body, query, and route params before reaching business logic.
- **Production Middleware**: Reusable patterns for global error handling, JWT authentication, async wrappers, and structured logging.
- **OWASP Security Defaults**: Best practices for `helmet`, CORS restriction, rate limiting, and safe payload boundaries.
- **TypeScript & JavaScript Starters**: Modular blueprints for rapid project scaffolding.

---

## Installation Guide

### Option 1: Install via `npx skills` (Recommended)

Add `express-skill` directly to your project workspace or global AI agent configuration:

```bash
# Add to your current project
npx skills add ac-aman/express-skill

# Or install specifically for Claude Code
npx skills add ac-aman/express-skill --agent claude-code

# Or install globally for all AI agents
npx skills add ac-aman/express-skill -g
```

### Option 2: Install via NPM

Install the package directly into your project dependencies:

```bash
npm install express-skill
```

Sync skills automatically using `npx skills`:
```bash
npx skills experimental_sync
```

---

## Supported AI Agents & Tools

This skill follows the open [`SKILL.md`](https://skills.sh) standard and is natively supported by:

- **Claude Code** & **Claude Desktop**
- **Cursor**
- **Antigravity**
- **GitHub Copilot**
- **Custom Agentic Workflows**

---

## License

[MIT](./LICENSE) © [ac-aman](https://github.com/ac-aman)
