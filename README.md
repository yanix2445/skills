# ⚡️ Yanix Skills Registry
> *Orchestrate your AI workflow with precision, elegance, and strictly typed patterns.*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Next.js](https://img.shields.io/badge/Next.js-16-black)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue)](https://www.typescriptlang.org/)

---

## 🚀 Quick Start

Inject highly-specialized skills directly into your **Cursor** or **Windsurf** environment using the power of `npx`.

> [!TIP]
> No cloning required. Just run the command in your project root.

```bash
# Install a specific skill (e.g., url-orchestrator)
npx skills add https://github.com/yanix2445/skills --skill url-orchestrator
```

### 📋 List Available Skills

Curious about what's available? List the registry content instantly:

```bash
npx skills add yanix2445/skills --list
```

---

## 📦 Skill Catalog

| Skill Name | Type | Impact | Description |
| :--- | :---: | :---: | :--- |
| **[`url-orchestrator`](url-orchestrator/SKILL.md)** | 🌐 | `HIGH` | Master your routing universe. **Next.js 16** aligned (Proxy, Parallel Routes), strictly typed (`Route`), and SEO-ready. |
| *More coming soon* | 🔜 | - | *Stay tuned for Auth, Payment, and UI skills.* |

---

## ✨ Features Breakdown

### 🌐 URL Orchestrator
> *The definitive guide to Next.js App Router governance.*

- [x] **Next.js 16 Compliant**: Native `proxy.ts`, `searchParams` (Async), and Typed Routes.
- [x] **Patterns Library**: Copy-paste ready examples for Intercepting Routes (`(.)folder`), Parallel Routes (`@folder`), and more.
- [x] **Strict Governance**: Rules for standardizing slugs, canonicals, and file structure.
- [ ] ~~Legacy Middleware support~~ (Deprecated)

<details>
<summary><strong>🔍 Peek into the architecture</strong></summary>

```mermaid
graph TD
    A[User Request] -->|Proxy.ts| B(Routing Layer)
    B --> C{Route Type}
    C -->|Static| D[Page]
    C -->|Dynamic| E[Layout]
    E --> F[Parallel @slot]
    E --> G[Intercepting (.)modal]
```
</details>

---

## 🛠 Usage & Workflow

1. **Install** the skill.
2. **Read** the `SKILL.md` to understand the governance.
3. **Apply** the patterns from `references/`.

> [!IMPORTANT]
> Always check for updates. The web evolves fast, and so do these skills.

---

<p align="center">
  <sub>Built with ❤️ by Yanis • Powered by <a href="https://github.github.com/gfm/">GFM</a></sub>
</p>
