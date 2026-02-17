# CLAUDE.md - Migration Project Context

## Project Overview

**Purpose:** Migrate from Clawdbot to PicoClaw on a smaller, cheaper server.
**Owner:** Sadiq (sdqmd)
**Status:** Planning phase — Ready for Phase 1a

## Key Decision

- **From:** Clawdbot (TypeScript, >1GB RAM, WhatsApp)
- **To:** PicoClaw (Go, <10MB RAM, Telegram)
- **Motivation:** Cost reduction (~€200/year savings)

## Migration Strategy (Revised)

**Phase 1: Test on Current Server (No Extra Cost)**
1. Install PicoClaw alongside Clawdbot on hamoodi-clawdbot
2. Run both side-by-side (WhatsApp + Telegram)
3. Copy workspace files, test functionality
4. Verify for 1-2 weeks

**Phase 2: Provision & Migrate**
1. Only after Phase 1 verified
2. Create cheap Hetzner CX23 (€3.49/mo)
3. Migrate PicoClaw + workspace there
4. Cutover, shutdown old server

## Why Revised?

- No extra cost during testing
- Easy side-by-side comparison
- Local file copy (no network transfer)
- No risk of paying for server that doesn't work

## Channel Change

- **Current:** WhatsApp (Clawdbot native)
- **Target:** Telegram (PicoClaw native)
- WhatsApp not supported in PicoClaw

## Target Server (Phase 2)

- **Provider:** Hetzner Cloud
- **Plan:** CX23 (cheapest x86)
- **Specs:** 2 vCPU, 4GB RAM, 40GB SSD
- **Cost:** €3.49/mo (with IPv4)
- **Location:** Any (latency not a concern)

## What Transfers

Compatible workspace files (direct copy):
- `AGENTS.md`, `SOUL.md`, `USER.md`, `IDENTITY.md`
- `memory/*.md`, `MEMORY.md`
- `TOOLS.md`

Config needs conversion (YAML → JSON format).

## What Doesn't Transfer

- WhatsApp integration (not supported)
- Browser/Canvas/Nodes (not needed)
- Clawdbot-specific features

## Open Questions

1. Telegram user ID for allowlist
2. Which API providers to configure
3. Server location preference (for Phase 2)
4. Timeline for Phase 1a

## Key Paths

**Current (Clawdbot):**
- Workspace: `~/clawd/`
- Config: `~/.clawdbot/`

**Target (PicoClaw):**
- Workspace: `~/.picoclaw/workspace/`
- Config: `~/.picoclaw/config.json`

## PicoClaw Resources

- Repo: https://github.com/sipeed/picoclaw
- Docs: See repo README
- Telegram setup: @BotFather, @userinfobot

## Related Projects

- OSDU System Design: `~/clawd/osdu-system-design/`
- Current Clawdbot workspace: `~/clawd/`

---

*Last updated: 2026-02-17*
