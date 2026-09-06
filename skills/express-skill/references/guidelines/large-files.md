---
title: Large Files & Code Organization Guideline
category: guidelines
---

# Large File and Code Organization

The generated codebase must prioritize maintainability, readability, and clear separation of responsibility.

---

## Core Rules

1. **Do not put unrelated responsibilities into the same file.**
2. **Do not create unnecessarily large controllers, services, repositories, routes, or utility files.**
3. **When a file becomes responsible for multiple independent operations, split it.**
4. **Group related operations into focused files or subdirectories.**
5. **Prefer several small cohesive files over one large file containing unrelated logic.**
6. **Do not split code artificially when the resulting files have no meaningful independent responsibility.**
7. **Preserve clear module ownership when splitting files. Related code should remain inside the same feature/module.**
8. **Avoid creating global folders containing hundreds of files from unrelated modules.**

---

## Split by Responsibility

If a file contains multiple independent responsibilities, split them.

### Bad Example
A single `users/user.repository.ts` containing:
- User creation logic
- User profile updates
- Password resets & authentication queries
- Search filtering
- Audit logging & reporting queries

### Preferred Example
Split into focused repositories:
```text
users/
└── repositories/
    ├── user-read.repository.ts
    ├── user-write.repository.ts
    ├── user-search.repository.ts
    └── user-auth.repository.ts
```

---

## Split Large Services by Capability or Use Case

### Bad Example
`user.service.ts` containing 1,500 lines handling every user operation in the system.

### Preferred Example
```text
users/
└── services/
    ├── create-user.service.ts
    ├── update-user.service.ts
    ├── delete-user.service.ts
    ├── authenticate-user.service.ts
    └── search-users.service.ts
```

For larger domains, group capability subfolders:

```text
users/
├── authentication/
│   ├── login.service.ts
│   ├── logout.service.ts
│   └── refresh-token.service.ts
│
├── profile/
│   ├── get-profile.service.ts
│   └── update-profile.service.ts
│
└── search/
    └── search-users.service.ts
```

---

## Do Not Split Based Only on Line Count

Line count is a warning signal, not an architectural trigger.

### Split a file when:
- It has multiple unrelated responsibilities.
- Functions belong to different business capabilities.
- Different parts change for different reasons.
- The file becomes difficult to navigate and reason about.
- Different parts have distinct external dependencies.
- The file contains multiple independent use cases.

### Do NOT split when:
- A single cohesive implementation is long due to necessary data validation or complex logic.
- Splitting creates fragment files with no independent purpose.

---

## Preserve Cohesion & Avoid Dumping Grounds

After splitting, each file should have a clear purpose that can be described in one short sentence.

- **Good**: `user-search.repository.ts` — *"Handles database queries required to search users."*
- **Bad**: `user-utils-2.ts` — *"Contains miscellaneous user functions."*

### Avoid Generic Dumping-Ground Files
Never create generic dumping ground files such as:
- `utils.ts`
- `helpers.ts`
- `common.ts`
- `misc.ts`
- `everything.ts`

Unless their responsibility is narrow, concrete, and explicitly well-defined (e.g. `date-formatter.ts` or `password-hasher.ts`).
