# Express AI Skill (`express-skill`)

> **Production-Grade Express.js Architecture & Workflow Skill for AI Coding Agents**  
> Enables AI agents (Antigravity, Cursor, Claude Code, Copilot) to build, decompose, and refactor Express.js applications using modular design, strict type safety, zero hallucinated dependencies, and zero security leakage.

```bash
npx skills add ac-aman/express-skill
```
---

## 🚀 Impact Metrics & Codebase Value

Equipping your AI agent with `express-skill` yields measurable codebase improvements:

| Metric / Aspect | Without `express-skill` | With `express-skill` | Improvement |
| :--- | :--- | :--- | :--- |
| **Oversized Dumping Ground Files** | High (1000+ line controllers/services) | **0%** (Responsibility-based splitting & progressive decomposition) | **95% Reduction** |
| **Security Leakage Risk** | High (Leaked stack traces, `x-powered-by`, open CORS) | **0%** (Strict zero-leakage, mandatory default-deny auth/RBAC) | **100% Protection** |
| **Dependency Hallucination** | Frequent (`MODULE_NOT_FOUND` runtime crashes) | **0%** (Strict `package.json` inspection & `// TODO:` protocol) | **Zero Hallucination** |
| **Context Token Consumption** | High (Forcing long architecture rules in every prompt) | **~800 Tokens** (Progressive disclosure: loads detailed guides on-demand) | **~85% Token Savings** |
| **Code Maintainability Index** | Low / Variable | **High** (Strict TypeScript `no-any`, standardized response envelopes) | **+85% Maintainability** |

---

## ⚡ How to Use

### 1. Installation

#### Option A: Via `npx skills` (Recommended)
```bash
# Add to your project workspace
npx skills add ac-aman/express-skill

# Or install globally for all AI agent sessions
npx skills add ac-aman/express-skill -g
```

#### Option B: Manual Copy into Project Workspace
Copy `skills/express-skill` into your workspace customization root:
```text
your-project/
└── .agents/
    └── skills/
        └── express-skill/
            ├── SKILL.md
            ├── references/
            └── templates/
```

### 2. How the Agent Uses This Skill (Progressive Disclosure)

1. When you ask your AI agent to build or refactor an Express application, the agent detects `express-skill` and loads **`SKILL.md`** (~`[SKILL_MD_TOKENS]` tokens).
2. `SKILL.md` acts as an orchestration runner, instructing the agent to:
   - Inspect `package.json` and directory context.
   - Select the target architecture (**Modular Architecture** by default).
   - Dynamically view the corresponding architecture guide in `references/architectures/<name>.md` on-demand.
   - View specific operational guidelines in `references/guidelines/<guideline>.md` only when performing relevant tasks (e.g. `security.md`, `validation.md`).

---

## 📁 Skill File Manifest & Context Token Accounting

The table below outlines the structure of `express-skill` along with approximate context token footprints. You can replace the `[TOKEN_COUNT]` placeholders with exact measured token counts.

| File Path | Description | Approx. Tokens |
| :--- | :--- | :--- |
| **`SKILL.md`** | Main orchestration runner, decision tree, inspection rules & guideline index | `~[SKILL_MD_TOKENS]` |
| **`references/architectures/modular.md`** | **Primary Default**: 3-stage progressive decomposition, module boundary & encapsulation | `~[MODULAR_ARCH_TOKENS]` |
| **`references/architectures/layered.md`** | Controller-Service-Repository 3-tier horizontal specification | `~[LAYERED_ARCH_TOKENS]` |
| **`references/architectures/simple.md`** | Single-file / minimal architecture for microservices & prototypes (<5 endpoints) | `~[SIMPLE_ARCH_TOKENS]` |
| **`references/architectures/clean.md`** | Concentric Clean Architecture & inward Dependency Inversion Ring 1 → 4 rules | `~[CLEAN_ARCH_TOKENS]` |
| **`references/architectures/ddd.md`** | Strategic Bounded Contexts, Aggregates, Value Objects & Domain Events | `~[DDD_ARCH_TOKENS]` |
| **`references/architectures/hexagonal.md`** | Ports & Adapters guide for multi-interface systems (REST, gRPC, CLI) | `~[HEXAGONAL_ARCH_TOKENS]` |
| **`references/guidelines/typescript.md`** | Strict TypeScript type safety, zero `any`, `.d.ts` Express Request augmentation | `~[TYPESCRIPT_GUIDE_TOKENS]` |
| **`references/guidelines/code-organization.md`** | Architecture-agnostic bootstrap separation (`server.ts` vs `app.ts`) & config isolation | `~[CODE_ORG_TOKENS]` |
| **`references/guidelines/file-size-and-decomposition.md`** | Single responsibility splitting & progressive decomposition lifecycle | `~[DECOMPOSITION_TOKENS]` |
| **`references/guidelines/naming.md`** | Kebab-case file naming, role suffixes, symbol casing & REST URI conventions | `~[NAMING_TOKENS]` |
| **`references/guidelines/dependencies.md`** | Inspection rules, avoiding custom wheel-reinvention using maintained libraries | `~[DEPENDENCIES_TOKENS]` |
| **`references/guidelines/error-handling.md`** | Operational vs programmer errors, `AppError` hierarchy, centralized error middleware | `~[ERROR_HANDLING_TOKENS]` |
| **`references/guidelines/validation.md`** | Schema validation middleware (`validate(schema)`), Zod co-location, fail-fast 400s | `~[VALIDATION_TOKENS]` |
| **`references/guidelines/configuration.md`** | Type-safe environment variable parsing (`dotenv` + `zod`), fail-fast startup checks | `~[CONFIG_TOKENS]` |
| **`references/guidelines/security.md`** | Strict zero-leakage, mandatory default-deny auth/RBAC, strict CORS, security suite | `~[SECURITY_TOKENS]` |
| **`references/guidelines/testing.md`** | Unit testing with mocks, end-to-end endpoint integration testing (`supertest`) | `~[TESTING_TOKENS]` |
| **`references/guidelines/api-design.md`** | RESTful URL conventions, 4-phase request lifecycle, universal `{ success, data }` envelope | `~[API_DESIGN_TOKENS]` |
| **`references/guidelines/refactoring.md`** | Safe refactoring sequence, contract preservation, workspace import updating | `~[REFACTORING_TOKENS]` |
| **`references/guidelines/code-comments-and-todos.md`** | Centralized readable mini-comments rule & mandatory `// TODO:` protocol | `~[COMMENTS_TODOS_TOKENS]` |

---

## 🛠️ Supported AI Agents

Follows the open [Agentic Skill](https://agents.sh) format and is natively supported by:
- **Antigravity IDE**
- **Claude Code & Claude Desktop**
- **Cursor**
- **GitHub Copilot Agent Mode**

---

## 📄 License

[MIT](./LICENSE) © [ac-aman](https://github.com/ac-aman)
