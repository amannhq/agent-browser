## Session Persistence & State Management

Closes vercel-labs/agent-browser#42

This PR implements session persistence with automatic state save/restore, encryption support, and comprehensive state management commands.

---

### New Features

#### 1. Session Persistence (`--session-name`)

Automatically save and restore cookies/localStorage across browser restarts:

```bash
# Auto-save/load state for "twitter" session
agent-browser --session-name twitter open twitter.com

# Login once, then state persists automatically
# State files stored in ~/.agent-browser/sessions/
```

#### 2. State Encryption (AES-256-GCM)

Encrypt sensitive session data at rest:

```bash
# Generate key: openssl rand -hex 32
export AGENT_BROWSER_ENCRYPTION_KEY=<64-char-hex-key>

# State files are now encrypted automatically
agent-browser --session-name secure open example.com
```

#### 3. State Management Commands

| Command | Description |
|---------|-------------|
| `state list` | List saved state files with size, date, encryption status |
| `state show <file>` | Show state summary (cookies, origins, domains) |
| `state rename <old> <new>` | Rename a state file |
| `state clear <session>` | Clear states for a specific session name |
| `state clear --all` | Clear all saved states |
| `state clean --older-than <days>` | Delete states older than N days |
| `state save <path>` | Manual save to custom path |
| `state load <path>` | Manual load from custom path |

#### 4. Auto-Expiration

Automatically clean up old state files:

```bash
export AGENT_BROWSER_STATE_EXPIRE_DAYS=7  # Default: 30

# Or manually clean old states
agent-browser state clean --older-than 7
```

#### 5. Session Name Validation

Security hardening to prevent path traversal attacks:
- Only alphanumeric, hyphens, underscores allowed
- Rejects `../`, spaces, slashes, special characters

---

### Environment Variables

| Variable | Description |
|----------|-------------|
| `AGENT_BROWSER_SESSION_NAME` | Auto-save/load state persistence name |
| `AGENT_BROWSER_ENCRYPTION_KEY` | 64-char hex key for AES-256-GCM encryption |
| `AGENT_BROWSER_STATE_EXPIRE_DAYS` | Auto-delete states older than N days (default: 30) |

---

### Changes

#### Rust CLI (`cli/src/`)

| File | Changes |
|------|---------|
| `commands.rs` | Parse new state commands (list, show, clear, clean, rename) |
| `flags.rs` | Add `--session-name` flag with validation |
| `output.rs` | Help text for new commands |
| `validation.rs` | New module for session name validation |
| `connection.rs` | Minor optimizations |

#### TypeScript (`src/`)

| File | Changes |
|------|---------|
| `daemon.ts` | Auto-load/save state on browser launch/close, auto-expiration cleanup on startup |
| `actions.ts` | Implement state list/show/clear/clean/rename handlers |
| `browser.ts` | Support encrypted state loading in launch |
| `encryption.ts` | New AES-256-GCM encryption module |
| `state-utils.ts` | Shared state file utilities |
| `protocol.ts` | New command schemas |
| `types.ts` | New command types |

#### Tests

| File | Changes |
|------|---------|
| `protocol.test.ts` | Tests for new state commands |
| Rust tests | Session name validation tests |

---

### Stats

```
 11 files changed, 1189 insertions(+), 8 deletions(-)
```

- 84 Rust tests (all passing)
- 131 TypeScript tests (all passing)

---

### Performance Optimizations

- Single-pass byte iteration for session name validation
- Optimized buffer parsing in daemon (avoid double string scan)
- Efficient line counting without array allocation
- Fast ref parsing with charCodeAt instead of regex
