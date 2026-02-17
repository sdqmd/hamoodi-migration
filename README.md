# Hamoodi Migration Plan

Migration from Clawdbot (OpenClaw) to PicoClaw on a smaller, cheaper server.

## Motivation

- **Cost reduction** — Current setup is overkill for actual usage
- **Simplicity** — Don't need Clawdbot's extensive features
- **Efficiency** — PicoClaw uses <10MB RAM vs >1GB

## Current vs Target

| Aspect | Current (Clawdbot) | Target (PicoClaw) |
|--------|-------------------|-------------------|
| **Software** | Clawdbot (TypeScript) | PicoClaw (Go) |
| **Server** | hamoodi-clawdbot | New Hetzner VPS |
| **RAM Usage** | >1GB | <10MB |
| **Channel** | WhatsApp | Telegram |
| **Cost** | ~€20+/mo | ~€3-4/mo |

## Target Server Options (Hetzner Cloud)

| Server | Type | vCPU | RAM | SSD | Price (IPv6) | Price (+IPv4) |
|--------|------|------|-----|-----|--------------|---------------|
| **CX23** | Intel/AMD | 2 | 4GB | 40GB | €2.99/mo | €3.49/mo |
| **CAX11** | ARM (Ampere) | 2 | 4GB | 40GB | €3.29/mo | €3.79/mo |

**Recommendation:** CX23 with IPv4 @ €3.49/mo
- PicoClaw needs <10MB RAM, so 4GB is massive overkill
- IPv4 needed for easier Telegram webhook setup
- x86 has broader compatibility if needed later

**Location Options:**
- NBG1 (Nuremberg, Germany)
- HEL1 (Helsinki, Finland)
- ASH (Ashburn, USA)
- HIL (Hillsboro, USA)
- SIN (Singapore)

*User notes: Latency not a concern, cheapest region acceptable.*

## Migration Strategy

```
Phase 1: Parallel Setup
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  Current Server                    New Server               │
│  (hamoodi-clawdbot)               (picoclaw-vps)            │
│                                                             │
│  ┌─────────────┐                  ┌─────────────┐           │
│  │  Clawdbot   │                  │  PicoClaw   │           │
│  │  WhatsApp   │                  │  Telegram   │           │
│  │  (primary)  │                  │  (testing)  │           │
│  └─────────────┘                  └─────────────┘           │
│                                                             │
│  Both running, test PicoClaw thoroughly                     │
└─────────────────────────────────────────────────────────────┘

Phase 2: Cutover
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  Old Server                        New Server               │
│  (shutdown)                       (picoclaw-vps)            │
│                                                             │
│  ┌─────────────┐                  ┌─────────────┐           │
│  │  Clawdbot   │                  │  PicoClaw   │           │
│  │   (off)     │ ───migrate───▶   │  Telegram   │           │
│  │             │   workspace      │  (primary)  │           │
│  └─────────────┘                  └─────────────┘           │
│                                                             │
│  Primary channel switches to Telegram                       │
└─────────────────────────────────────────────────────────────┘
```

## What Migrates

### ✅ Workspace Files (Compatible)

| File | Purpose | Migration |
|------|---------|-----------|
| `AGENTS.md` | Agent behavior | Direct copy |
| `SOUL.md` | Personality | Direct copy |
| `USER.md` | User info | Direct copy |
| `IDENTITY.md` | Agent identity | Direct copy |
| `TOOLS.md` | Tool notes | Direct copy |
| `memory/*.md` | Daily memories | Direct copy |
| `MEMORY.md` | Long-term memory | Direct copy |

### ⚠️ Config (Needs Conversion)

| Clawdbot | PicoClaw | Notes |
|----------|----------|-------|
| `~/.clawdbot/config.yaml` | `~/.picoclaw/config.json` | Different format |
| WhatsApp session | N/A | Not supported |
| API keys | API keys | Same keys, different config |

### ❌ Not Migrating

| Feature | Reason |
|---------|--------|
| WhatsApp | Not supported in PicoClaw |
| Browser control | Not needed |
| Canvas | Not needed |
| Nodes | Not needed |
| Complex cron | Basic cron in PicoClaw |

## Channel Change: WhatsApp → Telegram

**Why Telegram:**
- Native PicoClaw support
- Easy bot setup (just a token)
- Good mobile app
- No phone number linking hassle

**Setup:**
1. Message @BotFather on Telegram
2. Create new bot, get token
3. Add token to PicoClaw config
4. Start chatting

## Implementation Checklist

### Phase 1: Setup New Server

- [ ] Create Hetzner Cloud account (if needed)
- [ ] Provision CX23 server (cheapest region)
- [ ] Basic server setup (SSH keys, firewall)
- [ ] Install PicoClaw
- [ ] Create Telegram bot
- [ ] Configure PicoClaw with API keys
- [ ] Copy workspace files from current server
- [ ] Test basic functionality

### Phase 2: Parallel Testing

- [ ] Verify memory/context works
- [ ] Test tool execution (exec, read, write)
- [ ] Test web search
- [ ] Verify OSDU server SSH access works
- [ ] Run for 1-2 weeks in parallel
- [ ] Document any issues/differences

### Phase 3: Cutover

- [ ] Final workspace sync
- [ ] Update SSH keys if needed
- [ ] Switch primary usage to Telegram/PicoClaw
- [ ] Keep old server running 1 week (safety)
- [ ] Shutdown old server
- [ ] Update documentation

## Cost Comparison

| Item | Current | Target | Savings |
|------|---------|--------|---------|
| Server | ~€20/mo | €3.49/mo | €16.51/mo |
| **Annual** | ~€240/yr | €42/yr | **€198/yr** |

## Open Questions

1. **Telegram user ID** — Need your Telegram user ID for allowlist
2. **API keys** — Which providers to configure? (Anthropic, OpenRouter, etc.)
3. **Server location** — Any preference? (Default: cheapest)
4. **Timeline** — When to start Phase 1?

## Links

- [PicoClaw GitHub](https://github.com/sipeed/picoclaw)
- [Hetzner Cloud](https://www.hetzner.com/cloud)
- [Telegram BotFather](https://t.me/botfather)

---

*Status: Planning phase*
