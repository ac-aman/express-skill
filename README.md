# express-skill

> An open, production-ready AI Agent Skill for Express.js development. Compatible with `npx skills`, Antigravity, Claude Desktop, Cursor, and custom agentic AI systems.

---

## Overview

`express-skill` equips AI agents with standard patterns, clean 4-layer architecture workflows, production security defaults, and modular reference blueprints for Express.js applications.

---

## Features

- **Layered Architecture**: Enforces clear separation between Routes, Controllers, Services, and Repositories.
- **Strict Request Validation**: Integrated `zod` schemas for incoming payloads.
- **Production Middleware**: Reusable error handler, JWT auth middleware, async wrapper, and logger setup.
- **Security Defaults**: OWASP-aligned standards with `helmet`, `cors`, and `express-rate-limit`.
- **TypeScript & JavaScript Blueprints**: Pre-scaffolded starter code templates.

---

## Skill Directory Structure

```text
express-skill/
├── package.json             # NPM package manifest
├── README.md                # Skill documentation & publishing guide
├── LICENSE                  # MIT License
├── .gitignore               # Git & NPM ignore rules
└── skills/
    └── express-skill/
        ├── SKILL.md         # Core agent instructions & metadata (YAML frontmatter)
        ├── references/      # Detailed modular guides loaded on demand
        │   ├── architecture.md
        │   ├── middleware.md
        │   └── security.md
        └── templates/       # Boilerplate starters
            ├── express-ts-starter.md
            └── express-js-starter.md
```

---

## Installation & Usage

### Option 1: Via `npx skills` (GitHub Repository)
Push this repository to GitHub and install it into any workspace:
```bash
npx skills add ac-aman/express-skill
```

### Option 2: Via NPM (`npm install`)
Publish to NPM registry so agents or tools can discover it:
```bash
npm install express-skill
```

---

## Publishing Guide

### Step 1: Login to NPM
Ensure you have an account on [npmjs.com](https://www.npmjs.com/) and log in via CLI:
```bash
npm login
```

### Step 2: Validate Package Files
Test which files will be included in your npm tarball:
```bash
npm pack --dry-run
```

### Step 3: Publish to NPM
Publish the skill publicly:
```bash
npm publish --access public
```

---

## License

[MIT](./LICENSE)
