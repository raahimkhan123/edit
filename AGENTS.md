# AGENTS.md

## Cursor Cloud specific instructions

### Overview

FMHY (Free Media Heck Yeah) is a VitePress-based wiki/documentation site with a Nitro API backend (Cloudflare Worker). There is also a small `pests-repellent/` Cloudflare Worker for anti-framing.

### Tech Stack

- **Node.js** >= 25.2.1 (declared in `package.json` engines)
- **pnpm** 10.12.2 (via corepack, declared in `package.json` `packageManager`)
- **VitePress** for the docs site, **Nitro** for the API, **Wrangler** for Cloudflare Workers

### Running Services

Standard commands are in `package.json` scripts:
- `pnpm docs:dev` — VitePress docs dev server on port **5173**
- `pnpm api:dev` — Nitro API dev server on port **3000** (includes Cloudflare KV emulation)
- `pnpm format` — Prettier formatter/checker

### Non-obvious caveats

- **Node.js 25+ required**: The environment's default Node.js is too old. Use `nvm install 25 && nvm use 25` before running anything. Corepack must be installed globally (`npm install -g corepack`) since Node 25 removed it from the default distribution.
- **Build script approval**: pnpm's `onlyBuiltDependencies` in `package.json` must include `@parcel/watcher`, `@resvg/resvg-js`, `esbuild`, `sharp`, and `workerd`. Without these, the dev servers will fail. This change is committed in the setup branch.
- **pests-repellent is a separate workspace**: It has its own `pnpm-lock.yaml` and needs a separate `pnpm install` from `pests-repellent/`.
- **pests-repellent vitest tests**: The tests use `@cloudflare/vitest-pool-workers@0.10.x` which is incompatible with vitest 4.x. This is a pre-existing version mismatch; tests will not run until the dependency is updated.
- **`pnpm format` and package-lock.json**: The repo contains both `pnpm-lock.yaml` and a committed `package-lock.json`. Prettier fails to parse `package-lock.json`, causing the format command to exit with code 2. This is a pre-existing issue.
- **`pnpm docs:build`**: The production build has a pre-existing parse error in `ColorPicker.vue` (multiline `@click` handler). The dev server is unaffected.
