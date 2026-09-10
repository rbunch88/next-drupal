# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

next-drupal is a Next.js toolkit for Drupal. It provides a TypeScript client library (`next-drupal` npm package) and a Drupal module (`next`) that together enable using Next.js as a decoupled frontend for Drupal via JSON:API.

## Monorepo Structure

This is a Yarn workspaces monorepo using Turborepo for builds and Lerna for versioning/publishing.

- **`packages/next-drupal/`** — Core TypeScript client library (npm package). Three entry points: `src/index.ts`, `src/draft.ts`, `src/navigation.ts`. Built with tsup to dual ESM/CJS.
- **`modules/next/`** — Drupal module (PHP). Provides JSON:API enhancements and decoupled routing.
- **`www/`** — Documentation site (next-drupal.org). Next.js + MDX + TypeDoc for API docs.
- **`examples/`** — Example Next.js apps demonstrating various integration patterns.
- **`starters/`** — Starter templates (basic, graphql, pages router).
- **`drupal/`** — Local Drupal installation for development/testing.

## Key Commands

```bash
yarn install                  # Install all workspace dependencies
yarn test                     # Run next-drupal Jest tests (requires .env, see below)
yarn lint                     # ESLint across the repo
yarn format:check             # Prettier check
yarn format                   # Prettier fix
yarn phpcs                    # PHP Code Sniffer for Drupal module
yarn test:next                # PHPUnit tests for Drupal module
yarn test:e2e:ci              # Cypress E2E tests (requires Drupal database/files)

# Workspace-specific
yarn workspace next-drupal dev      # Watch mode for the client library
yarn workspace next-drupal test     # Run client library tests
yarn workspace www dev              # Run docs site on port 4444
```

### Running a Single Test

```bash
yarn workspace next-drupal test -- --testNamePattern="pattern"
```

### Test Environment

Tests require a running Drupal instance. Copy the env template before running:
```bash
cp packages/next-drupal/.env.example packages/next-drupal/.env
```

Required env vars: `DRUPAL_BASE_URL`, `DRUPAL_USERNAME`, `DRUPAL_PASSWORD`, `DRUPAL_CLIENT_ID`, `DRUPAL_CLIENT_SECRET`.

## Client Library Architecture (`packages/next-drupal/src/`)

Class hierarchy:
- **`NextDrupalBase`** — Base class handling auth, fetching, URL construction, token management.
- **`NextDrupal`** extends `NextDrupalBase` — App Router client. JSON:API operations (getResource, getResourceCollection, createResource, etc.), menu trees, views, path translation. Default API prefix: `/jsonapi`.
- **`NextDrupalPages`** extends `NextDrupal` — Pages Router additions (getStaticProps helpers, preview mode, getStaticPathsFromContext).

Other key modules:
- `draft.ts` — Draft mode utilities (App Router)
- `navigation.ts` — Navigation/routing helpers
- `menu-tree.ts` — `DrupalMenuTree` class for hierarchical menus
- `jsonapi-errors.ts` — `JsonApiErrors` error handling class
- `deprecated/` — Legacy standalone functions (pre-class API)

The library uses `jsona` for JSON:API deserialization and `qs` for query string building.

## Code Style

- **Prettier**: No semicolons, ES5 trailing commas. Drupal module override: semicolons + single quotes.
- **Commits**: Conventional Commits format — `<type>(<scope>): <subject>`. Scopes match directory names (e.g., `next-drupal`, `next`, `basic-starter`).
- **Coverage**: Jest enforces 100% coverage on non-deprecated source files.
- **Node**: v18.19 (see `.nvmrc`).
- **Package manager**: Yarn 1.22.15 (`yarn`, not npm/pnpm).
