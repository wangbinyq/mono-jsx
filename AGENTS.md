# mono-jsx

mono-jsx is a JSX runtime that renders a `<html>` JSX element into a `Response` object, with optional client-side runtime pieces (signals, router, RPC, forms, and so on) bundled for the browser.

## Project structure

```text
mono-jsx/
├── runtime/             # Client runtime (signals, router, rpc, form, etc...)
├── test/                # Deno tests (*.test.tsx)
├── types/               # Type definitions
├── build.ts             # bundles package modules to *.mjs       # SSR: vnode → HTML Response
├── setup.ts             # Setup CLI: `npx mono-jsx setup`
├── version.ts           # Generated from package.json — do not edit
└── *.ts                 # mono-jsx SSR Core (index.ts, jsx-runtime.ts, jsx.ts, render.ts, symbols.ts)
```

## Build

Required before publishing to npm (`prepublishOnly` runs this) and after edits to `build.ts`, `runtime/*.ts`, or packaging entrypoints that feed `*.mjs`:

```bash
deno task build
```

The build writes `index.mjs`, `jsx-runtime.mjs`, `setup.mjs`, regenerates `runtime/index.ts` and `version.ts`, and refreshes `bin/mono-jsx`.

## Testing

Full suite (runs `build` first, then all tests under `test/`):

```bash
deno task test
```

Single file:

```bash
deno task build
deno test -A --no-lock test/<name>.test.tsx
```

To run a single test file with a specific filter:

```bash
deno task build
deno test -A --no-lock test/<name>.test.tsx --filter "<search>"
```

## Linting and formatting

```bash
deno lint
deno task fmt    # dprint; same as: dprint fmt
```

## Publishing

Publishing is automated by GitHub Actions on `main` when a version tag is pushed.

When the user asks to publish a new version:

0. Run `deno task test` first, **if the tests fail, stop the process and notify the user.**
1. Check the current version in `package.json`.
2. Show the user a selectable list of possible next versions, ask the user to choose which one to publish.
3. After the user confirms the target version, update both `package.json` and `version.ts`.
4. Run `deno task build` to build the package dist.
5. Commit only the release-related changes with `git commit -m "chore: bump package version to v<new-version>"`.
6. Create a git tag named `v<new-version>` on that commit.
7. Show the user a prompt window to confirm the publish action, and if the user confirms, push both the commit and tag to the remote repository.
