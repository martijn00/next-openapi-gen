# Contributing to next-openapi-gen

Thanks for helping improve `next-openapi-gen`.

## Before you start

- Use Node.js 18 or newer.
- The repository itself currently uses npm and `package-lock.json`.
- Keep changes focused and update documentation when you change CLI behavior, config fields, or example usage.

## Local setup

```bash
git clone https://github.com/YOUR_USERNAME/next-openapi-gen.git
cd next-openapi-gen
npm install
```

Create a branch for your work:

```bash
git checkout -b feat/my-change
```

## Project layout

```text
next-openapi-gen/
├── src/        # package source
├── tests/      # Vitest coverage
├── examples/   # example Next.js apps
└── dist/       # build output
```

## Development workflow

Run the checks that match your change before opening a pull request:

```bash
npm test
npm run build
npx tsc --noEmit
```

Useful extras:

```bash
npm run test:watch
npm run test:ui
npm run test:coverage
```

## Pull requests

Pull request titles should use the Conventional Commits format because the repository uses squash merge.

Examples:

```text
feat: add support for custom response sets
fix: resolve pages router method detection
docs: simplify README quick start
```

When you open a PR:

- summarize the user-facing change and why it matters
- list the commands or manual checks you ran
- update `README.md`, example docs, or changelog-related documentation when behavior changed

## Commit message guide

Use a conventional commit type that matches the intent of the change:

| Type | Use for |
| --- | --- |
| `feat` | new functionality |
| `fix` | bug fixes |
| `docs` | documentation-only changes |
| `refactor` | internal restructuring without behavior changes |
| `test` | test-only updates |
| `build` | package, tooling, or build pipeline updates |
| `ci` | CI workflow changes |
| `chore` | maintenance work |

Breaking changes should use `!` in the type or include a `BREAKING CHANGE:` footer.

## Documentation expectations

Keep repo-facing docs accurate:

- `README.md` should describe the current CLI surface and supported config
- example READMEs should match the commands used in that example
- `CONTRIBUTING.md` and the pull request template should match the actual contributor workflow

Prefer accurate documentation over aspirational documentation. If a script or workflow does not exist yet, do not document it as if it does.

## Releases

Only maintainers should cut releases.

Before running the release command, manually run the normal verification steps:

```bash
npm test
npm run build
npx tsc --noEmit
```

Then run:

```bash
npm run release
```

Current release behavior:

- uses `np`
- skips tests during the `np` run itself
- updates `CHANGELOG.md` through the `version` lifecycle script

If you change the release flow, update this file, `README.md`, and the relevant scripts together.

## Questions

- Open a GitHub issue for bugs
- Open a GitHub discussion for usage questions or broader ideas

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
