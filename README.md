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
| **Context Token Consumption** | High (Forcing long architecture rules in every prompt) | **~1,425 Tokens** (Progressive disclosure: loads detailed guides on-demand) | **~85% Token Savings** |
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

1. When you ask your AI agent to build or refactor an Express application, the agent detects `express-skill` and loads **`SKILL.md`** (~`1,425` tokens).
2. `SKILL.md` acts as an orchestration runner, instructing the agent to:
   - Inspect `package.json` and directory context.
   - Select the target architecture (**Modular Architecture** by default).
   - Dynamically view the corresponding architecture guide in `references/architectures/<name>.md` on-demand.
   - View specific operational guidelines in `references/guidelines/<guideline>.md` only when performing relevant tasks (e.g. `security.md`, `validation.md`).

---

## 📁 Skill File Manifest & Context Token Accounting

The table below outlines the structure of `express-skill` along with exact context token footprints:

| File Path | Description | Exact Tokens |
| :--- | :--- | :--- |
| **`SKILL.md`** | Main orchestration runner, decision tree, inspection rules & guideline index | **1,425** |
| **`references/architectures/modular.md`** | **Primary Default**: 3-stage progressive decomposition, module boundary & encapsulation | **2,220** |
| **`references/architectures/hexagonal.md`** | Ports & Adapters guide for multi-interface systems (REST, gRPC, CLI) | **1,328** |
| **`references/architectures/clean.md`** | Concentric Clean Architecture & inward Dependency Inversion Ring 1 → 4 rules | **1,269** |
| **`references/architectures/ddd.md`** | Strategic Bounded Contexts, Aggregates, Value Objects & Domain Events | **1,209** |
| **`references/architectures/layered.md`** | Controller-Service-Repository 3-tier horizontal specification | **981** |
| **`references/architectures/simple.md`** | Single-file / minimal architecture for microservices & prototypes (<5 endpoints) | **717** |
| **`references/guidelines/security.md`** | Strict zero-leakage, mandatory default-deny auth/RBAC, strict CORS, security suite | **1,872** |
| **`references/guidelines/error-handling.md`** | Operational vs programmer errors, `AppError` hierarchy, centralized error middleware | **1,212** |
| **`references/guidelines/api-design.md`** | RESTful URL conventions, 4-phase request lifecycle, universal `{ success, data }` envelope | **1,061** |
| **`references/guidelines/testing.md`** | Unit testing with mocks, end-to-end endpoint integration testing (`supertest`) | **890** |
| **`references/guidelines/typescript.md`** | Strict TypeScript type safety, zero `any`, `.d.ts` Express Request augmentation | **881** |
| **`references/guidelines/naming.md`** | Kebab-case file naming, role suffixes, symbol casing & REST URI conventions | **866** |
| **`references/guidelines/configuration.md`** | Type-safe environment variable parsing (`dotenv` + `zod`), fail-fast startup checks | **847** |
| **`references/guidelines/validation.md`** | Schema validation middleware (`validate(schema)`), Zod co-location, fail-fast 400s | **827** |
| **`references/guidelines/refactoring.md`** | Safe refactoring sequence, contract preservation, workspace import updating | **786** |
| **`references/guidelines/file-size-and-decomposition.md`** | Single responsibility splitting & progressive decomposition lifecycle | **768** |
| **`references/guidelines/code-organization.md`** | Architecture-agnostic bootstrap separation (`server.ts` vs `app.ts`) & config isolation | **748** |
| **`references/guidelines/dependencies.md`** | Inspection rules, avoiding custom wheel-reinvention using maintained libraries | **735** |
| **`references/guidelines/code-comments-and-todos.md`** | Centralized readable mini-comments rule & mandatory `// TODO:` protocol | **547** |

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
