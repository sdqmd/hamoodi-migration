# CLAUDE.md - Migration Project Context

## Project Overview

**Purpose:** Migrate from Clawdbot to PicoClaw on a smaller, cheaper server.
**Owner:** Sadiq (sdqmd)
**Status:** Planning phase — Ready for Phase 1a

## Key Decision

- **From:** Clawdbot (TypeScript, >1GB RAM, WhatsApp)
- **To:** PicoClaw (Go, <10MB RAM, Telegram)
- **Motivation:** Cost reduction (~€200/year savings)

## User Requirements

- Does NOT need Clawdbot's extensive features
- Fine with switching from WhatsApp to Telegram
- Doesn't mind latency (any server region OK)
- Wants parallel testing before cutover
- Prioritizes cost savings

## Migration Strategy (Revised)

### Phase 1: Test on Current Server (No Extra Cost)
1. Install PicoClaw alongside Clawdbot on hamoodi-clawdbot
2. Run both side-by-side (WhatsApp + Telegram)
3. Copy workspace files, test functionality
4. Verify for 1-2 weeks

### Phase 2: Provision & Migrate
1. Only after Phase 1 verified
2. Create cheap Hetzner CX23 (€3.49/mo)
3. Migrate PicoClaw + workspace there
4. Cutover, shutdown old server

### Why This Approach?
- No extra cost during testing (both on same server)
- Easy side-by-side comparison
- Local file copy (no network transfer)
- No risk of paying for server that doesn't work

## Channel Change

- **Current:** WhatsApp (Clawdbot native)
- **Target:** Telegram (PicoClaw native)
- WhatsApp not supported in PicoClaw
- User confirmed OK with Telegram

## Target Server (Phase 2)

| Spec | Value |
|------|-------|
| Provider | Hetzner Cloud |
| Plan | CX23 (cheapest x86) |
| vCPU | 2 |
| RAM | 4GB (overkill for PicoClaw's 10MB) |
| SSD | 40GB |
| Cost | €3.49/mo (with IPv4) |
| Location | Any region (latency not a concern) |

## Cost Savings

| | Current | Target | Savings |
|---|---------|--------|---------|
| Monthly | ~€20 | €3.49 | €16.51 |
| Annual | ~€240 | €42 | **€198** |

## What Transfers

Compatible workspace files (direct copy):
```
~/clawd/AGENTS.md      → ~/.picoclaw/workspace/AGENTS.md
~/clawd/SOUL.md        → ~/.picoclaw/workspace/SOUL.md
~/clawd/USER.md        → ~/.picoclaw/workspace/USER.md
~/clawd/IDENTITY.md    → ~/.picoclaw/workspace/IDENTITY.md
~/clawd/TOOLS.md       → ~/.picoclaw/workspace/TOOLS.md
~/clawd/MEMORY.md      → ~/.picoclaw/workspace/MEMORY.md
~/clawd/memory/        → ~/.picoclaw/workspace/memory/
```

Config needs conversion (Clawdbot YAML → PicoClaw JSON).

## What Doesn't Transfer

| Feature | Reason |
|---------|--------|
| WhatsApp | Not supported in PicoClaw |
| Browser control | Not needed |
| Canvas | Not needed |
| Nodes | Not needed |
| Complex cron | Basic cron in PicoClaw |

## Implementation Checklist Summary

**Phase 1a:** Install PicoClaw binary, create Telegram bot, configure
**Phase 1b:** Copy workspace files, verify loading
**Phase 1c:** Test tools (exec, read, write, SSH, web search)
**Phase 2:** Extended testing 1-2 weeks
**Phase 3:** Provision Hetzner CX23, migrate
**Phase 4:** Cutover, shutdown old server

## Open Questions

1. **Telegram user ID** — Need for allowlist config
2. **API providers** — Anthropic? OpenRouter? Which to configure?
3. **Timeline** — When to start Phase 1a?

## Key Paths

**Current (Clawdbot):**
- Workspace: `~/clawd/`
- Config: `~/.clawdbot/`
- Server: hamoodi-clawdbot

**Target (PicoClaw):**
- Workspace: `~/.picoclaw/workspace/`
- Config: `~/.picoclaw/config.json`

## PicoClaw Resources

- Repo: https://github.com/sipeed/picoclaw
- Install: Single binary, `picoclaw onboard` to init
- Telegram: @BotFather (create bot), @userinfobot (get user ID)

## Related Projects

- OSDU System Design: `~/clawd/osdu-system-design/`
- Current Clawdbot workspace: `~/clawd/`

## Quick Start Commands

```bash
# Install PicoClaw
wget https://github.com/sipeed/picoclaw/releases/latest/download/picoclaw-linux-amd64 -O picoclaw
chmod +x picoclaw && sudo mv picoclaw /usr/local/bin/
picoclaw onboard

# Copy workspace
cp ~/clawd/{AGENTS,SOUL,USER,IDENTITY,TOOLS,MEMORY}.md ~/.picoclaw/workspace/
cp -r ~/clawd/memory ~/.picoclaw/workspace/

# Start
picoclaw gateway
```

---

*Last updated: 2026-02-17*
