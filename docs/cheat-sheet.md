# Software Factories — Quick Reference Cheat Sheet

> Living document. Updated as each course section is completed.
> Course: *Software Factories for Real Engineers* (Cohort 005)
> Sources: repo README, `AGENTS.md`, `docs/agents/`, `.claude/skills/`, and the `live-run-through` checkpoint history.

---

## Setup

### Quick Check

```bash
node --version   # v22 or later
git --version
gh auth status   # must be logged in
jq --version
```

### Getting Started

```bash
# 1. Clone the course repo
git clone https://github.com/ai-hero-dev/cohort-005-project.git
cd cohort-005-project

# 2. Fork it into your own GitHub repo (in place)
npx ai-hero-cli fork

# 3. Install dependencies
npm install

# 4. Database
npm run db:migrate
npm run db:seed

# 5. Dev server
npm run dev          # http://localhost:5173 — DevUI floating panel switches users
```

### What `fork` does

- Replaces the folder's git history with a single fresh commit on `main`.
- Creates a new **public** repo on your GitHub account, pushed as `origin`.
- Public on purpose: GitHub Actions minutes are free on public repos — the factory runs there.
- Your issues live on **your** repo, never the course repo.

### Plan & Model

| Topic | Guidance |
| :--- | :--- |
| Model | Written for Opus-level models (Claude Opus on Max). Budget **$100/month minimum** — the factory adds usage on top of your own. |
| Not using Claude | Codex/OpenCode/Pi need a per-token API key in CI. GitHub Copilot works with a fine-grained PAT (`COPILOT_GITHUB_TOKEN`, Copilot Requests permission); pick a model your plan includes. |
| Signups later | **Knock** (free Developer tier) for notifications — keys go in gitignored `.env`. **tldraw** for whiteboarding tasks. |

---

## Scripts

| Command | Description |
| :--- | :--- |
| `npm run dev` | Start the development server |
| `npm run build` | Build for production |
| `npm run test` | Run tests (Vitest) |
| `npm run test:watch` | Tests in watch mode |
| `npm run typecheck` | Type-check (runs `react-router typegen` first) |
| `npm run db:generate` | Generate a migration from the schema |
| `npm run db:migrate` | Run migrations |
| `npm run db:seed` | Reseed (drops + recreates the DB) |
| `npm run reset <slug>` | Reset repo to a lesson checkpoint (destructive — asks first) |
| `npm run cherry-pick <slug>` | Cherry-pick a lesson's solution |
| `npm run pull` | Merge the course repo's latest `main` in |

Lesson checkpoints live on the `live-run-through` branch, addressed by **slug**. After a reset/cherry-pick, if in doubt: `npm install` (if lockfile changed), `npm run db:migrate`, `npm run db:seed`.

---

## Session Basics (carried over from the Crash Course)

| Action | How | Why |
| :--- | :--- | :--- |
| Clear context | `/clear` | Resets the conversation — fresh session, same repo |
| Check context usage | `/context` | Bar chart of what's eating the window — stay out of the "dumb zone" (~40%+) |
| Check plan usage | `/usage` | Quota remaining for session/week |
| Plan mode | `Shift+Tab` (cycle) | Agent plans + grills you before touching code |
| Rewind | `Esc` `Esc` | Restore code and/or conversation to an earlier point |
| Resume | `claude --continue` / `/resume` | Sessions persist locally |
| Reference files | `@file` + `Tab` | Pull files straight into context |
| Verbose mode | `Ctrl+O` | See sub-agent activity and exact commands |

---

## The Eight Sections

| # | Section | What it covers |
| :--- | :--- | :--- |
| 1 | **Before We Start** | Repo setup, choosing a model, Discord, office hours |
| 2 | **Domain Language** | A shared vocabulary: glossary + ADRs you and the agent both use |
| 3 | **Wayfinder** | Charting the map of the work before building: research, spikes, prototypes |
| 4 | **Review** | Reviewing what your agent produces (and making it fix what you find) |
| 5 | **Codebase Design** | Shaping the codebase so agents can work in it: deep modules, boundaries |
| 6 | **Feedback Loops** | Automated checks that catch mistakes before you do: standards, hooks, mutation testing |
| 7 | **Software Factory Part One** | Running your agent in GitHub Actions |
| 8 | **Software Factory Part Two** | Choosing the factory's infrastructure and designing the jobs it does |

### Checkpoint map (from `live-run-through`)

- **Domain Language** — create the glossary and fix naming drift (`Refund`); grow the glossary until it's a problem, then prune it; same for the ADR pile; add precise terms (`Person`, `Country Lock`, `Notification`, `Discussion Reply`) with ADR-0013.
- **Wayfinder** — add the wayfinder/prototype/research skills; time-boxed research docs (vendor comparison, web-push reach) that get **removed** after informing the plan; Knock spike → removed; Notification Feed prototype → removed; then ship in-app notifications for real.
- **Review** — add the PR skill; ship bookmarks + notes in one diff; fix the review findings; add the retro skill.
- **Codebase Design** — fold every service into its routes (feel the pain first); comment-drift rule; codebase-design + deep-modules skills; untestable date dependency; extract a module from a loader; wire the deep module boundary; move every permission decision into one permissions module.
- **Feedback Loops** — seed bloated coding standards → split and prune to three real rules; file-length rule → Husky pre-commit hook → Prettier → faster hook with more checks → oxlint; custom lint rule; courses search with `sql.raw`; Stryker mutation testing scoped to the diff (+ incremental mode); kill the surviving mutant; diagnosing-bugs skill; let the agent drive a browser.
- **Factory Part One** — CI workflow on PRs (added, then replaced); dev-only feedback button that files a GitHub issue; factory workflows; reviewer that fixes more and lists findings once; chain the factory end-to-end: **filed issue → agent → reviewed PR**.
- **Factory Part Two** — the `software-factory` skill: infrastructure choice and job design.

---

## Skills in this repo (`.claude/skills/`)

| Skill | Purpose |
| :--- | :--- |
| `ask-matt` | Router — asks which skill or flow fits your situation |
| `grill-me` / `grilling` | Relentless interview to stress-test a plan or decision |
| `to-spec` | Turn the conversation into a spec, published to the issue tracker |
| `to-tickets` | Break a plan/spec into tracer-bullet tickets (each declares its blockers) |
| `implement` / `implement-spec` | Implement work from a spec or set of tickets |
| `tdd` | Red-green-refactor, test-first features and bug fixes |
| `code-review` | Review changes since a fixed point on two axes: **Standards** + **Spec** (parallel sub-agents) |
| `database-migrations` | Schema change → generate migration → update seed → reseed |
| `handoff` | Compact the conversation into a handoff doc for the next agent |
| `teach` | Teach a concept within this workspace |
| `writing-for-agents` | How to write skills, `AGENTS.md`, `CLAUDE.md` |
| `setup-matt-pocock-skills` | Sync skills from upstream |

The course adds 15 more along the way — **all 15 are installed** (copied from the course's `live-run-through` branch): `domain-modeling`, `wayfinder`, `prototype`, `research`, `wizard`, `pr`, `retro`, `codebase-design`, `setup-ts-deep-modules`, `improve-codebase-architecture`, `mutation-testing`, `diagnosing-bugs`, `grill-with-docs`, `knock-workflows`, `software-factory`.

---

## Issue Tracker (GitHub, via `gh`)

Issues and PRDs live as GitHub issues **on your fork** (`origin`), managed with `gh`.

| Operation | Command |
| :--- | :--- |
| Create | `gh issue create --title "..." --body "..."` (heredoc for multi-line) |
| Read | `gh issue view <n> --comments` |
| List | `gh issue list --state open --json number,title,body,labels,comments --jq '...'` |
| Comment | `gh issue comment <n> --body "..."` |
| Labels | `gh issue edit <n> --add-label "..."` / `--remove-label "..."` |
| Close | `gh issue close <n> --comment "..."` |

**Gotcha:** `gh` doesn't reliably pick `origin` — it prefers a remote named `upstream`, which `npm run reset`/`cherry-pick` add pointing at the course repo. Check with `gh repo set-default --view`; fix with `gh repo set-default <origin owner/repo>` (or pass `-R owner/repo`).

---

## Verification Checks (from `AGENTS.md`)

Before reporting any `app/` change as complete, **in this order**:

1. `npm run typecheck`
2. `npm run test`

### When something breaks

| Symptom | Cause / Fix |
| :--- | :--- |
| 401 "Select a user from the DevUI panel" | No user in session — switch user + country in the floating DevUI panel (`app/components/dev-ui.tsx`) |
| `tsc` says every `./+types/*` import is missing | Typegen hasn't run — use `npm run typecheck` (runs `react-router typegen` first) |
| "Could not locate the bindings file" (better-sqlite3) | Native binding never built — often `ignore-scripts=true` in your npm config. Fix: `npm rebuild better-sqlite3 --ignore-scripts=false` |

---

## The Factory (Part One → Part Two)

The end state the course builds: a chain that runs **without you watching** —

```
filed GitHub issue → agent picks it up → implements → opens PR
       → reviewer agent reviews (Standards + Spec) → fixes findings
       → checks run (typecheck, tests, hooks, mutation testing) → reviewed PR
```

- Factory workflows live in `.github/workflows/` and run Claude Code on your subscription (or `COPILOT_GITHUB_TOKEN` for Copilot).
- The dev-only **feedback button** files a GitHub issue straight from the running app.
- The `software-factory` skill (Part Two) covers choosing infrastructure and designing the jobs the factory should do.
