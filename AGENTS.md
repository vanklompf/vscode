# VS Code Agents Instructions

This file provides instructions for AI coding agents working with the VS Code codebase.

For detailed project overview, architecture, coding guidelines, and validation steps, see the [Copilot Instructions](.github/copilot-instructions.md).

## Cursor Cloud specific instructions

### Environment overview

- **Node.js**: v22.22.x (specified in `.nvmrc`; major must match, minor.patch must be >=)
- **npm**: < 11.2.0 (enforced by `build/npm/preinstall.ts`)
- **Package manager**: npm only (yarn is explicitly rejected)
- **Native deps** (Linux): `libsecret-1-dev`, `libxkbfile-dev`, `libkrb5-dev`, `pkg-config`, `gcc`, `g++`, `make`, `python3`, `patch`

### Key commands

| Task | Command |
|------|---------|
| Install deps | `npm install` (postinstall handles ~60 sub-directories) |
| Download Electron | `npm run electron` |
| Full compile | `npm run compile` |
| Dev watch mode | `npm run watch` |
| Type-check src/ | `npm run compile-check-ts-native` |
| Compile extensions | `npm run gulp compile-extensions` |
| Lint | `npm run eslint` |
| Unit tests (Node) | `npm run test-node` |
| Unit tests (Browser) | `npm run test-browser` |
| Integration tests | `scripts/test-integration.sh` |
| Launch Code OSS | `VSCODE_SKIP_PRELAUNCH=1 ./scripts/code.sh` (skip prelaunch if already compiled) |

### Gotchas

- `npm install` uses an install-state hash. If deps haven't changed since last successful install, it exits instantly. To force a full reinstall: `VSCODE_FORCE_INSTALL=1 npm install`.
- The preinstall step downloads node-gyp headers for both Electron (root `.npmrc`) and Node (remote `.npmrc`), and on Linux applies a V8 header patch. If the patch fails (e.g., already applied), you may need `VSCODE_FORCE_INSTALL=1`.
- Code OSS requires a display. Use `export DISPLAY=:1` with xvfb (`Xvfb :1 &`) if no X server is running. The VM already has xvfb installed.
- When launching Code OSS, unset `ELECTRON_RUN_AS_NODE` if it's in the environment.
- Code OSS does NOT hot-reload. After recompiling, you must kill and relaunch the Electron process.
- dbus errors in the console are expected in headless environments and can be ignored.
