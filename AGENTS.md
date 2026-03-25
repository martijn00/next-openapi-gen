# AGENTS.md

## Repo overview

- The publishable package lives in `src/` and is built into `dist/`.
- Tests live in `tests/` and run with Vitest.
- Example Next.js apps live in `examples/`.

## Working rules

- Keep repo-facing docs accurate. Do not document scripts, CI steps, or release flows that do not exist in the current branch.
- When CLI flags, config shape, or supported schema behavior changes, update `README.md` and any affected example README files in the same change.
- Keep pull request titles in Conventional Commits format because the repository uses squash merge.

## Validation

- For code changes, run `npm test`, `npm run build`, and `npx tsc --noEmit`.
- For docs-only changes, verify commands, paths, and links against the current repository state.
- Prefer the smallest relevant check set, but do not claim checks were run if they were not.

## Files to avoid changing casually

- Do not edit `dist/` by hand.
- Do not touch lockfiles unless the change actually updates dependencies.
- Do not rewrite generated example output unless the behavior changed and you intentionally regenerated it.
