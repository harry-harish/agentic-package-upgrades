# Agentic Package Upgrades

A GitHub Actions pipeline that autonomously handles non-backward-compatible package upgrades — from codebase impact analysis to test writing, migration, code review response, and a merge-ready PR.

**Claude** does the upgrade and responds to code review. **CodeRabbit** does the automated review. **GitHub Actions** orchestrates everything. A human provides domain context at the start and makes the final merge call.

→ Full writeup: [We Automated the Package Upgrade Nobody Wanted to Do](https://personal-swart-pi.vercel.app/writing/agentic-package-upgrades-claude-coderabbit-github-actions)

---

## How it works

```
Phase 0 (Human)  →  Phase 1 (Claude)  →  Phase 2 (CodeRabbit)  →  Phase 3 (Claude)  →  Phase 4 (Human)
Write BC doc         Upgrade cycle         Code review              Respond to review      Merge
```

**Phase 0 — Human:** Write a breaking-change doc. Open a PR with a metadata table.

**Phase 1 — Claude:** Reads the breaking-change doc, runs impact analysis across the entire codebase, writes tests for every BREAKING usage, applies the migration, validates, posts a migration report.

**Phase 2 — CodeRabbit:** Automatically reviews all of Claude's changes. Classifies inline comments as BLOCKING / SUGGESTION / NITPICK / FALSE_POSITIVE.

**Phase 3 — Claude:** Fetches all CodeRabbit inline comments (paginated), fixes BLOCKING and SUGGESTION items with commits and inline replies, explains NITPICK and FALSE_POSITIVE calls. Capped at 3 rounds.

**Phase 4 — Human:** Reviews the migration report and CodeRabbit resolution state. Merges. Agents cannot merge.

---

## Setup

### 1. Prerequisites

- [Anthropic API key](https://console.anthropic.com) (Claude runs via `anthropics/claude-code-action`)
- [CodeRabbit](https://coderabbit.ai) installed on your repo (free tier works)
- GitHub Actions enabled

### 2. Copy the workflow files

Copy `.github/workflows/upgrade-cycle.yml` and `.github/workflows/upgrade-review-responder.yml` into your repo.

### 3. Add the secret

In your repo → Settings → Secrets and variables → Actions:

```
ANTHROPIC_API_KEY    your Anthropic API key
```

`GITHUB_TOKEN` is provided automatically by Actions — no setup needed.

### 4. Add `CLAUDE.md` to your repo (recommended)

Copy `CLAUDE.md` from this repo and fill it in. Claude reads this at the start of every upgrade cycle to understand your repo's conventions, test setup, and any patterns to watch out for. Without it, Claude will infer context from the codebase — which works, but is less reliable.

### 5. Write a breaking-change doc

Copy `docs/breaking-changes/TEMPLATE.md` for each package you're upgrading. Fill in:
- Every changed API with the old and new signatures
- Ripgrep patterns to find usages in the codebase
- Test scenarios to validate the migration

This doc is the human's domain knowledge contribution. It's what makes the pipeline reliable. Budget 30–60 minutes for a thorough doc.

### 6. Open a PR

Include this metadata table in the PR description:

```markdown
| Package | From | To | Breaking-change doc |
|---|---|---|---|
| your-package | 1.x | 2.x | docs/breaking-changes/your-package/1.x-to-2.x.md |
```

The CI pipeline parses this table to bootstrap Phase 1.

---

## Customizing Phase 3

The decision criteria for what counts as BLOCKING vs. NITPICK vs. FALSE_POSITIVE live in the prompt inside `upgrade-review-responder.yml`. If CodeRabbit reliably misclassifies certain patterns in your codebase, add a note to that prompt — Claude will handle them correctly from the next run.

The 3-round cap is tracked via PR labels (`upgrade-cycle-round-1`, `upgrade-cycle-round-2`, `upgrade-cycle-round-3`). Adjust the `>= 3` check in `check-reviewer` to change the limit.

---

## Re-triggering manually

If Claude stalls at any step, comment `@claude` on the PR with instructions. The `claude-code-action` supports interactive triggering via PR comments.

---

## Validated use cases beyond package upgrades

- **CVE and security upgrades** — same pipeline, higher urgency. Write the breaking-change doc from the CVE advisory.
- **Repo health reports** — run Phase 1 (analysis only) without migration. Output: structured report of outdated dependencies, breaking-change surface, estimated effort.
- **Continuous documentation** — Phase 1 reads recent commits and diffs against `CLAUDE.md` / `repo-context.md`, posts a PR with updates. CodeRabbit reviews.

---

## License

MIT
