# CLAUDE.md – Development Workflow

This file records how I like development to be done. Always work according to it.

**How this folder is used:** start Claude Code in this `FullStack` folder, because `.claude/settings.json` is read only from the folder where the session starts. Each project lives in its own subfolder, and "project root" means that subfolder, not `FullStack`. To start a session inside a project instead, copy `.claude/` into it: a project with its own git repository does not see the agents above its repository root. `CHANGELOG.md` and `VERSION.md` in this folder track this configuration only. Each project keeps its own.

## 1. Language

**Strict rule: everything that is written down is in English. Never ask me about it, never use any other language.**

- **All documentation** you create, whether I ask for it or it comes up during development, is written in English.
- **All code is in English**: names, variables, functions, classes, files, comments, log and error messages, commit messages, everything.
- **All other documents are in English too**: summaries, bug findings, collections, improvements, feature ideas, change log, version file, notes, anything.
- This applies every single time, without exception and without asking me first.
- **Exception, chat:** replies to me in the conversation follow the language I write in.
- **Exception, product text:** marketing copy and user-facing text may be in another language when I explicitly ask for that language. Such text may appear in `COPY.md` and in the product's user-facing strings and translation files. Everything else stays English.

## 2. Change log and versioning

- **After every task that changes files, update the change log.** No exceptions.
- If there is no change log yet, create one (`CHANGELOG.md`) and keep a version file (`VERSION.md`) next to it. The two must always stay in sync.
- Every change belongs to **one version number**. Multiple changes can live under the same version.
- Every entry must include a **date** and a **description of the change**.
- Versioning: `MAJOR.MINOR.PATCH`. A fix bumps PATCH, a new feature bumps MINOR. Ask only before a MAJOR bump, or when the size is unclear.

Example format:

```markdown
## [1.2.0]
### 2026-11-08
- New feature: ...
- Fix: ...
```

## 3. Git and committing

- **I always commit manually, myself.** Never commit, and **never ask whether you should commit**. Only commit if I explicitly ask you to.
- Git is otherwise **read-only** for you: `status`, `diff`, `log` are fine. Never create branches, stash, merge or push.

## 4. Testing and verification

- After a change is done (new feature, fix, anything), **ask whether I want you to run tests / verify the implementation**.
- **Never run tests automatically**, only if I say yes.
- **Do not write automated tests (unit/integration) unless I ask for them.** This overrides any plugin, skill or default that asks for tests.
- A smoke check after a local deployment is not a test: see section 20.

## 5. Asking questions when unsure

- If the task is clear and relatively simple, **do not ask**, just do it.
- If you are unsure, don't know which solution is right, or the task or its solution has many possible outcomes, **always ask** before implementing.
- When asking, **propose a concrete answer** (as the recommended option), but also leave room for me to write my own answer.

## 6. Communication

- Be **concise and simple**.
- At the end of every implementation, give a **short summary**: what happened, what you did, what changed.
- The summary also states **what was not checked / any open risk**, and suggests the **next step** when there is a sensible one.

## 7. Scope: full stack development

- "Development" means the whole full stack: frontend, backend, database, messaging systems, and anything else that belongs to it.
- There is **no fixed tech stack**, it differs per project. Take the stack from the existing code. If it is not obvious, ask.
- Follow the project's existing structure, layers, naming and patterns. In an empty project, propose a simple, clean structure.

## 8. Code principles

- Use a sensible **balance of KISS, YAGNI and SOLID**: the simplest working solution, no speculative features, and SOLID where it genuinely helps.
- Add abstractions only when they are really needed.

## 9. Size of changes

- Refactoring is allowed **when justified**.
- Before a **large restructuring**, ask first.
- If you notice something unrelated (a bug, a better approach), mention it in the summary instead of silently changing it.

## 10. Dependencies

- **Always ask before adding a new dependency** (package, library), with a proposed answer.
- By default, use the existing code and the standard library.

## 11. Database

- You may **write** migrations, but **never run them without asking me first**. The one exception is the local deployment on my machine (section 20).
- Never touch live/production data without my explicit approval.

## 12. Security

- **Never hardcode secrets** (passwords, API keys, tokens). Use environment variables or a secret store.
- **Always validate at system boundaries**: API inputs, forms, external data. Use parameterized SQL and check authentication/authorization on new endpoints.
- If you notice a security risk in existing code, **flag it in the summary**, but do not fix it without asking.

## 13. Destructive operations

- **Always ask before** deleting files or folders, overwriting, `git reset`/force push, mass renames, or deleting data.
- Say exactly what will happen when asking.

## 14. Error handling and logging

- Use **explicit error handling**. Never swallow errors silently.
- Write meaningful error messages.
- Use the project's existing logging solution.
- Never log sensitive data (passwords, tokens, personal data).

## 15. APIs and external interfaces

- Keep **backward compatibility** for existing endpoints, DTOs and event/message schemas.
- **Ask before making a breaking change**, and record it in the change log.

## 16. Frontend, UI and design

- Reuse existing components and the design system. Before building a new UI element, check whether a similar one already exists.
- Follow accessibility (a11y) basics: semantic HTML, labels, keyboard support, sufficient contrast.
- New interfaces must be responsive (mobile and desktop).
- The default style is **modern, clean and professional, yet with marketing appeal** that catches people's attention.
- I have no fixed colors, design patterns or design documents. **The design choices are up to you**, guided by the goals above.
- **All design-related files always live in the `_design` folder** at the project root. If `_design` has files, follow them. Otherwise follow the design already in the code (components, colors, patterns).
- If you are unsure about a design question, **always ask before implementing**, with a proposed answer.

## 17. Deployment, infrastructure and environments

- You may **write and modify** Dockerfiles, pipelines, IaC and config files.
- **Never run or deploy them** outside my local machine, and never change infrastructure or call external services, unless I ask separately. The only exception is the local deployment in section 20.
- Environment-specific values live in config or environment variables, **never in the code**.
- Do not touch production or staging resources (databases, APIs, servers) unless I explicitly ask.

## 18. Performance

- Avoid obvious problems (N+1 queries, needless re-renders, missing indexes).
- Do not optimize prematurely. Optimize only when I ask.

## 19. Code comments

- Comment only the **why**: where the reason for a decision is not visible from the code.
- Use meaningful names so the code explains itself.

## 20. Local deployment (Docker Desktop and Kubernetes)

- I work locally with **Docker Desktop**. Dockerfiles and Docker Compose run there, and a **local Kubernetes** cluster is running in the background too.
- After finishing an implementation, fix or new feature that should be tried out, **deploy it locally automatically, without asking**: use Docker / Docker Compose or Kubernetes, depending on the project's architecture. This applies to my local machine only.
- **Local-only guards.** If any guard fails or cannot be confirmed, stop and ask:
  - Kubernetes: always name the context explicitly, `kubectl --context docker-desktop` and `helm --kube-context docker-desktop`. Never rely on the current context.
  - Docker: `docker context show` must be the local Docker Desktop context (`desktop-linux` or `default`), and `DOCKER_HOST` must be unset.
  - Database: before running a migration, confirm the target host is local (`localhost`, `127.0.0.1`, `host.docker.internal`, or a service in the local Compose project or cluster). Check it from the Compose file or with a command that prints only the host, never the password.
- **Database migrations may run automatically** as part of the local deployment, against the local database only, and only when they are **non-destructive**. A migration that drops or deletes data still needs asking first (section 13). The same goes for removing Docker volumes.
- After deploying, run a **smoke check**: containers or pods are running, and the health endpoint or main page responds. This is not a test (section 4). Report a failure instead of calling the deployment done.
- Tell me in the summary what was deployed and how to reach it.

## 21. Authentication

When an application needs login or authentication, always plan for these two options by default:

- **Username/password**, always available locally. Passwords must always be **hashed properly** (a strong, salted, adaptive algorithm such as Argon2 or bcrypt, never plain text or a fast hash).
- **Google authentication (Google sign-in)**, always offered alongside it. Without Google credentials the local environment must still start, with the Google option turned off.
- **Account linking:** never link a Google identity to an existing account by matching email alone. Require `email_verified` and proof of ownership of the existing account, so nobody can take over an account by registering its email first.
- Keep secrets (such as the Google client secret) in environment variables or a secret store, never in the code (see section 12).
- For a **production version or production configuration**, always ask whether to keep username/password, use only Google authentication, or something else. Do not decide this on your own.

## 22. Longer, multi-step tasks

- Before starting, give me a **short, simply worded plan**: what you are going to do and why.
- **Wait for my approval** before you begin. Do not start until I accept it.
- Small, simple tasks do not need a separate plan.

## 23. Bug fixing and getting stuck

- Fix the **root cause**, not the symptom. First understand what causes the bug, then fix it there. Briefly state the cause in the summary.
- Do not just guess. Look for the root cause from a few different angles.
- If it still does not work after **2-3 attempts**, stop. Summarize what you tried, then ask me, with a proposed answer.

## 24. Agents

Specialist agents live in `.claude/agents/`. Use the matching agent when I ask for it by name or role. All agents follow this file.

| Agent | Role | Writes |
|---|---|---|
| `backend-developer` | Backend: APIs, security, messaging, architecture | Backend code |
| `frontend-developer` | Frontend: components, state, responsive, a11y | Frontend code |
| `ux-ui-designer` | UX/UI design, user flows, design system | `_design/` |
| `database-expert` | Schema, migrations, queries, integrity | Schema, migrations, queries, seed data |
| `qa-tester` | Writes and runs tests, reports bugs | Tests, test fixtures and config, `BUGS.md` |
| `technical-writer` | README, API docs, user guides | Documentation files not owned by another agent |
| `marketing-copywriter` | Website copy and UI microcopy | `COPY.md` |
| `cybersecurity-analyst` | Security review and plans, no fixes | `SECURITY_PLAN.md`, `SECURITY_FINDINGS.md` |

**The main Claude** owns `CHANGELOG.md`, `VERSION.md`, and Docker, Compose, Kubernetes and pipeline files. After an agent finishes, the main Claude updates the change log, runs the local deployment (section 20) and gives the summary.

### Rules for all agents

- You cannot ask the user directly. If you are unsure, or a task has many possible outcomes, stop and return your questions to the caller, each with a recommended answer.
- Ask the caller before: adding a dependency, a breaking API change, a large restructuring, a destructive operation, or running anything outside the local machine.
- Write only what your row in the table allows. Never edit this file, `.claude/`, or a file owned by another agent or the main Claude. You may read and link to them.
- Read the actual code, config and docs before you write or judge. Never invent facts or findings.
- **Report** when you finish: what you did and what changed, the root cause if it was a bug, what was not checked and any open risk, and the next suggested step if there is a sensible one.

### Review mode

When asked to review the current state, an agent reviews instead of changing anything:

- Make no changes unless asked. Return the result in the reply, not in a file.
- Return one list of findings, ordered from **quick wins first** (simple, fast, easy to fix) to **complex solutions last**.
- For each item give: a short title, where it is, what is wrong and why it matters, the suggested fix, and the effort (quick win, small, medium, complex).
- Mark each finding as confirmed or suspected. Report only real findings. If there are none, say so.
