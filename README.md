name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build-test-lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '2
          cache: 'pnpm'
      - name: Use pnpm
        run: |
          corepack enable
          corepack prepare pnpm@latest --activate
      - name: Install deps
        run: pnpm install --frozen-lockfile=false
      - name: Lint
        run: pnpm lint
      - name: Build
        run: pnpm build
      - name: Test
        run: pnpm test -- --run
