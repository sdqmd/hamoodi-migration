# CLAUDE.md - Migration Project Context

## Project Overview

**Purpose:** Migrate from Clawdbot to PicoClaw on a smaller, cheaper server.
**Owner:** Sadiq (sdqmd)
**Status:** Planning phase

## Key Decision

- **From:** Clawdbot (TypeScript, >1GB RAM, WhatsApp)
- **To:** PicoClaw (Go, <10MB RAM, Telegram)
- **Motivation:** Cost reduction (~€200/year savings)

## Migration Strategy

1. **Parallel first** — Run both systems simultaneously
2. **Test thoroughly** — Verify PicoClaw works for all needed tasks
3. **Cutover** — Switch primary usage once verified

## Channel Change

- **Current:** WhatsApp (Clawdbot native)
- **Target:** Telegram (PicoClaw native)
- WhatsApp not supported in PicoClaw

## Target Server

- **Provider:** Hetzner Cloud
- **Plan:** CX23 (cheapest x86)
- **Specs:** 2 vCPU, 4GB RAM, 40GB SSD
- **Cost:** €3.49/mo (with IPv4)

## What Transfers

Compatible workspace files:
- `AGENTS.md`, `SOUL.md`, `USER.md`, `IDENTITY.md`
- `memory/*.md`, `MEMORY.md`
- `TOOLS.md`

Config needs conversion (YAML → JSON format).

## What Doesn't Transfer

- WhatsApp integration (not supported)
- Browser/Canvas/Nodes (not needed)
- Clawdbot-specific features

## Open Questions (Documented in README)

1. Telegram user ID for allowlist
2. Which API providers to configure
3. Server location preference
4. Timeline for Phase 1

## PicoClaw Resources

- Repo: https://github.com/sipeed/picoclaw
- Config: `~/.picoclaw/config.json`
- Workspace: `~/.picoclaw/workspace/`

## Related

- Current Clawdbot server: hamoodi-clawdbot
- OSDU project: See `~/clawd/osdu-system-design/`

---

*Last updated: 2026-02-17*
