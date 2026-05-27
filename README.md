# Meteor pnpm Workspace

A minimal Meteor app inside a pnpm monorepo.

This skeleton keeps Meteor apps in `apps/` and shared code in `packages/`.
The app imports the workspace packages with `workspace:*` dependencies.

## Requirements

- Meteor 3.4 onwards
- Rspack integration (see [Modern Build Stack docs](https://docs.meteor.com/about/modern-build-stack/rspack-bundler-integration.html))

## Structure

```text
.
├── apps/
│   └── app/             # Meteor application
├── packages/
│   ├── domain/          # Shared client/server helpers
│   ├── server-tools/    # Server-only helper package
│   └── ui/              # Client UI helper package
└── pnpm-workspace.yaml
```

## Run

```sh
corepack pnpm install
cd apps/app
meteor run
```

Open http://localhost:3000.

## What This Demonstrates

- `apps/app/package.json` depends on local packages with `workspace:*`.
- `apps/app/rspack.config.cjs` compiles workspace package sources through Rspack.
- The browser renders content imported from `@example/ui` and `@example/shared`.
- The server imports `@example/shared` and `@example/server`.
- `@example/shared` uses the `color` npm package. That package has its own
  chain of dependencies, and pnpm keeps them hidden inside its internal store
  (`node_modules/.pnpm/`) instead of copying them to the top of the project.
  The accent color is calculated when the file first loads on both the client
  and the server, so if pnpm cannot find any package in that chain the app
  will not even start.

The Meteor app sets `meteor.autoInstallDeps` to `false` because pnpm handles
all npm installs for this workspace.

## pnpm Gotchas

- If a workspace package imports another workspace package, list it under
  `dependencies` in its `package.json`. npm and yarn often let you skip this
  because they copy packages around; pnpm does not, and the build will fail.
- Do not set `resolve.symlinks: false` in `rspack.config.cjs`. pnpm reaches
  every dependency through symlinks into `node_modules/.pnpm/`, so turning
  symlink following off makes most packages unreachable.
