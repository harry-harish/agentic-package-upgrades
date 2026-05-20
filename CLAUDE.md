# Repo Context for Upgrade Agent

This file is read by Claude at the start of every upgrade cycle (Phase 1, Step A).
Fill in the sections below so Claude understands your repo before touching anything.

---

## Repo Overview

[Describe what this repo does in 2-3 sentences. Include the tech stack and primary language.]

Example: This is a Node.js REST API service using Express. It handles file uploads and
processing for [product]. Primary language: JavaScript (CommonJS modules).

---

## Test Setup

[How to run tests. Claude needs this to execute Step B (baseline) and Step F (validation).]

```bash
# Command to run full test suite
npm test

# Command to run a single test file (if needed)
npm test -- --testPathPattern=filename
```

Test framework: [jest / mocha / vitest / other]

---

## Code Conventions

[Anything Claude should know before writing or modifying code.]

- Module system: CommonJS (`require`) / ESM (`import`)
- Async pattern: callbacks / Promises / async-await
- Error handling convention: [describe]
- File naming: [describe]

---

## Patterns to Be Aware Of

[Usage patterns that might look like a breaking-change hit but aren't, or vice versa.]

Example:
- `formidable` is only used in `src/middleware/upload.js` — ignore any hits in `__mocks__/`
- The `IncomingForm` class is also re-exported in `src/utils/form-helpers.js` — that file
  needs to be migrated too, not just the direct usages

---

## Off-Limits

[Files or directories Claude must not modify.]

- `vendor/` — third-party code checked in directly, do not touch
- `generated/` — auto-generated, changes will be overwritten
