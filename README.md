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
| **Server** | hamoodi-clawdbot (64GB) | New Hetzner CX23 (4GB) |
| **RAM Usage** | >1GB | <10MB |
| **Channel** | WhatsApp | Telegram |
| **Cost** | ~€20+/mo | ~€3.49/mo |

## Migration Strategy (Revised)

```
Phase 1: Side-by-Side Testing (Same Server)
┌─────────────────────────────────────────────────────────┐
│              Current Server (hamoodi-clawdbot)          │
│                                                         │
│  ┌─────────────────┐      ┌─────────────────┐           │
│  │    Clawdbot     │      │    PicoClaw     │           │
│  │                 │      │                 │           │
│  │  WhatsApp       │      │  Telegram       │           │
│  │  ~/clawd/       │      │  ~/.picoclaw/   │           │
│  │                 │      │    workspace/   │           │
│  │  (primary)      │      │  (testing)      │           │
│  └─────────────────┘      └─────────────────┘           │
│                                                         │
│  Both running on same server, no extra cost             │
└─────────────────────────────────────────────────────────┘

Phase 2: Provision & Migrate (After Verification)
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Old Server                    New Server               │
│  (hamoodi-clawdbot)           (Hetzner CX23)            │
│                                                         │
│  ┌─────────────────┐          ┌─────────────────┐       │
│  │   Clawdbot      │          │   PicoClaw      │       │
│  │   (shutdown)    │ ──────▶  │   Telegram      │       │
│  │                 │ migrate  │   (primary)     │       │
│  └─────────────────┘ workspace└─────────────────┘       │
│                                                         │
│  Cheap server only after everything verified            │
└─────────────────────────────────────────────────────────┘
```

### Why This Approach?

| Aspect | Original Plan | Revised Plan |
|--------|---------------|--------------|
| Cost during testing | €3.49/mo extra | €0 (same server) |
| Workspace copy | Network transfer | Local copy |
| Comparison testing | 2 servers | Same box, easy |
| Risk | Paying before verified | No cost until ready |

## Target Server (Phase 2)

| Server | Type | vCPU | RAM | SSD | Price |
|--------|------|------|-----|-----|-------|
| **CX23** | Intel/AMD | 2 | 4GB | 40GB | €3.49/mo |

- PicoClaw needs <10MB RAM, so 4GB is massive overkill
- IPv4 included for Telegram webhook
- Any Hetzner region (latency not a concern)

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

## Channel Change: WhatsApp → Telegram

**Why Telegram:**
- Native PicoClaw support
- Easy bot setup (just a token from @BotFather)
- Good mobile app
- No phone number linking hassle

## Implementation Checklist

### Phase 1a: Install PicoClaw on Current Server

- [ ] Download PicoClaw binary for x86_64
- [ ] Create config at `~/.picoclaw/config.json`
- [ ] Create Telegram bot via @BotFather
- [ ] Configure Telegram token and user allowlist
- [ ] Add API keys (Anthropic/OpenRouter)
- [ ] Start PicoClaw gateway
- [ ] Test basic chat via Telegram

### Phase 1b: Workspace Setup

- [ ] Copy workspace files to `~/.picoclaw/workspace/`
  ```bash
  cp ~/clawd/AGENTS.md ~/.picoclaw/workspace/
  cp ~/clawd/SOUL.md ~/.picoclaw/workspace/
  cp ~/clawd/USER.md ~/.picoclaw/workspace/
  cp ~/clawd/IDENTITY.md ~/.picoclaw/workspace/
  cp ~/clawd/TOOLS.md ~/.picoclaw/workspace/
  cp ~/clawd/MEMORY.md ~/.picoclaw/workspace/
  cp -r ~/clawd/memory ~/.picoclaw/workspace/
  ```
- [ ] Verify PicoClaw loads workspace correctly
- [ ] Test memory recall works

### Phase 1c: Feature Verification

- [ ] Test tool execution (exec, read, write)
- [ ] Test web search
- [ ] Test file operations
- [ ] Verify SSH to OSDU server works
- [ ] Verify SSH to dev server works
- [ ] Test cron/scheduled tasks (if needed)

### Phase 2: Extended Testing

- [ ] Run both systems for 1-2 weeks
- [ ] Use Telegram for daily tasks
- [ ] Document any issues or differences
- [ ] Verify stability

### Phase 3: Provision New Server

- [ ] Create Hetzner CX23 (cheapest region)
- [ ] Basic setup (SSH keys, firewall)
- [ ] Install PicoClaw
- [ ] Copy workspace from current server
- [ ] Configure Telegram (same bot token)
- [ ] Verify everything works

### Phase 4: Cutover

- [ ] Final workspace sync
- [ ] Update any SSH keys if needed
- [ ] Switch primary usage to new server
- [ ] Keep old server 1 week (safety net)
- [ ] Shutdown old server
- [ ] Update documentation

## Cost Comparison

| Item | Current | Target | Savings |
|------|---------|--------|---------|
| Server | ~€20/mo | €3.49/mo | €16.51/mo |
| **Annual** | ~€240/yr | €42/yr | **€198/yr** |

## Open Questions

1. **Telegram user ID** — Need your Telegram user ID for allowlist
2. **API keys** — Which providers? (Anthropic, OpenRouter, etc.)
3. **Server location** — Any preference? (Default: cheapest available)
4. **Timeline** — When to start Phase 1a?

## Quick Commands Reference

### Install PicoClaw (Current Server)

```bash
# Download latest release
wget https://github.com/sipeed/picoclaw/releases/latest/download/picoclaw-linux-amd64 -O picoclaw
chmod +x picoclaw
sudo mv picoclaw /usr/local/bin/

# Initialize
picoclaw onboard

# Edit config
vim ~/.picoclaw/config.json

# Start gateway
picoclaw gateway
```

### Create Telegram Bot

1. Open Telegram, message @BotFather
2. Send `/newbot`
3. Follow prompts, get token
4. Get your user ID from @userinfobot

### PicoClaw Config Template

```json
{
  "agents": {
    "defaults": {
      "workspace": "~/.picoclaw/workspace",
      "model": "claude-3-5-sonnet-20241022",
      "max_tokens": 8192
    }
  },
  "providers": {
    "anthropic": {
      "api_key": "sk-ant-..."
    }
  },
  "channels": {
    "telegram": {
      "enabled": true,
      "token": "YOUR_BOT_TOKEN",
      "allowFrom": ["YOUR_USER_ID"]
    }
  }
}
```

## Links

- [PicoClaw GitHub](https://github.com/sipeed/picoclaw)
- [Hetzner Cloud](https://www.hetzner.com/cloud)
- [Telegram BotFather](https://t.me/botfather)
- [Telegram UserInfoBot](https://t.me/userinfobot)

---

*Status: Planning phase — Ready for Phase 1a*
