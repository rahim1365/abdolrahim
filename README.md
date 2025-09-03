name: Rust CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:latest
    runs-on: ubuntu-
    steps:
      - uses: actions/checkout@v4
      - name: Install Rust
        uses: actions-rs/toolchain@v1
        with:
          toolchain: stable
          override: true

      - name: Build
        run: cargo build --verbos

      - name: Run tests
        run: cargo test --verbose

      - name: lint
        run: cargo clippy -- -D warnings
