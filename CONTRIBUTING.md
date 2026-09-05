# Contributing to express-skill

Thank you for your interest in contributing to `express-skill`! We welcome contributions from the community to help make AI coding assistants smarter, safer, and more effective at building Express.js applications.

---

## How You Can Contribute

You can contribute in several ways:

1. **Improve Existing Skill Guidelines**: Enhance instructions, architecture rules, or security practices in [`skills/express-skill/SKILL.md`](./skills/express-skill/SKILL.md).
2. **Add Modular References**: Add technical guides for databases (Prisma, Mongoose), testing (Supertest, Vitest), or OAuth/Auth in [`skills/express-skill/references/`](./skills/express-skill/references/).
3. **Add Boilerplate Blueprints**: Add starter templates in [`skills/express-skill/templates/`](./skills/express-skill/templates/).
4. **Add New Bundled Skills**: Add new complementary agent skills inside the `skills/` directory (e.g. `skills/prisma-skill/SKILL.md`).

---

## Development Workflow

### 1. Fork & Clone
Fork this repository to your GitHub account and clone it locally:

```bash
git clone https://github.com/YOUR-USERNAME/express-skill.git
cd express-skill
```

### 2. Create a Feature Branch
Create a descriptive branch for your changes:

```bash
git checkout -b feature/add-prisma-reference
```

### 3. Make Your Changes
- Ensure all `SKILL.md` files include valid **YAML frontmatter**:
  ```yaml
  ---
  name: express-skill
  description: A short description of the skill capability.
  license: MIT
  metadata:
    author: "your-username"
    version: "1.0.0"
    tags: ["express", "node"]
  ---
  ```
- Follow clean Markdown formatting and standard Express.js best practices.

### 4. Validate Package Payload
Test your changes to ensure all files will be included when published:

```bash
npm pack --dry-run
```

---

## Pull Request Guidelines

1. Push your branch to your forked repository:
   ```bash
   git push origin feature/add-prisma-reference
   ```
2. Open a **Pull Request** against the `main` branch of `ac-aman/express-skill`.
3. Provide a clear description of the changes or new skill guidelines added.

---

## Code of Conduct & License

By contributing to `express-skill`, you agree that your contributions will be licensed under the project's [MIT License](./LICENSE).
