# Development Setup Guide

This guide will help you set up the agent-browser project for local development.

## Prerequisites

✓ **Node.js** v20+ (you have v24.11.1)
✓ **pnpm** v8+ (you have v10.24.0)
✓ **Rust** 1.70+ (you have v1.91.1)

## Setup Steps

### 1. Install Dependencies

```bash
pnpm install
```

This will:
- Install all Node.js dependencies
- Set up Husky git hooks
- Attempt to download pre-built native binary (may fail for development versions)

### 2. Build TypeScript Code

```bash
pnpm build
```

This compiles the TypeScript source code from `src/` to `dist/`.

### 3. Build Native Rust CLI (Optional but Recommended)

```bash
pnpm build:native
```

This compiles the Rust CLI for faster startup times. The binary will be placed in:
- `bin/agent-browser-darwin-arm64` (macOS Apple Silicon)
- `bin/agent-browser-darwin-x64` (macOS Intel)
- `bin/agent-browser-linux-x64` (Linux)
- `bin/agent-browser-win-x64.exe` (Windows)

### 4. Install Chromium Browser

```bash
npx playwright install chromium
```

On Linux, you may need system dependencies:
```bash
npx playwright install --with-deps chromium
```

## Development Workflow

### Run in Development Mode

```bash
pnpm dev
```

This runs the daemon with `tsx` for hot-reloading during development.

### Run Tests

```bash
# Run all tests once
pnpm test

# Watch mode for TDD
pnpm test:watch
```

### Type Checking

```bash
pnpm typecheck
```

### Code Formatting

```bash
# Format code
pnpm format

# Check formatting
pnpm format:check
```

## Project Structure

```
agent-browser/
├── src/              # TypeScript source code
│   ├── daemon.ts     # Main daemon entry point
│   ├── browser.ts    # Browser automation logic
│   ├── protocol.ts   # Protocol parsing
│   └── types.ts      # Type definitions
├── cli/              # Rust CLI source
│   ├── src/          # Rust source files
│   └── Cargo.toml    # Rust dependencies
├── bin/              # CLI executables
├── dist/             # Compiled TypeScript output
├── test/             # Test files
└── docs/             # Documentation site
```

## Testing Your Changes

### Quick Manual Test

```bash
# Start daemon in one terminal
pnpm dev

# In another terminal, test commands
node dist/daemon.js
```

Or use the CLI directly:

```bash
./bin/agent-browser-darwin-arm64 open example.com
./bin/agent-browser-darwin-arm64 snapshot
./bin/agent-browser-darwin-arm64 close
```

### Running Specific Tests

```bash
# Run browser tests
pnpm test src/browser.test.ts

# Run protocol tests
pnpm test src/protocol.test.ts
```

## Building for Multiple Platforms

```bash
# Build for Linux (uses Docker)
pnpm build:linux

# Build for macOS (requires macOS with cross-compilation tools)
pnpm build:macos

# Build for Windows (uses Docker)
pnpm build:windows

# Build for all platforms
pnpm build:all-platforms
```

## Common Development Tasks

### Making Changes to TypeScript Code

1. Edit files in `src/`
2. Run `pnpm build` or use `pnpm dev` for hot-reloading
3. Test with `pnpm test`
4. Format with `pnpm format`

### Making Changes to Rust CLI

1. Edit files in `cli/src/`
2. Run `pnpm build:native`
3. Test the binary: `./bin/agent-browser-darwin-arm64 <command>`

### Adding New Dependencies

```bash
# Add Node.js dependency
pnpm add <package>

# Add dev dependency
pnpm add -D <package>

# Add Rust dependency
cd cli && cargo add <crate>
```

## Troubleshooting

### Native binary not found
If you see "Could not download native binary", run:
```bash
pnpm build:native
```

### Playwright/Chromium issues
Reinstall Chromium:
```bash
npx playwright install --force chromium
```

### Build errors
Clean and rebuild:
```bash
rm -rf dist node_modules
pnpm install
pnpm build
pnpm build:native
```

## Current Setup Status

✓ All dependencies installed
✓ TypeScript code compiled to `dist/`
✓ Native binary built: `bin/agent-browser-darwin-arm64`
✓ Chromium browser installed
✓ Tests passing (120 passed)

You're ready to start developing!

## Next Steps

1. Read the [README.md](./README.md) for usage examples
2. Check out [docs/](./docs) for detailed documentation
3. Look at existing tests in `src/*.test.ts` for examples
4. Try the CLI: `./bin/agent-browser-darwin-arm64 open example.com`