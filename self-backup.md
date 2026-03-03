# Self-Backup Setup Prompt

> **Usage:** Paste this into your OpenClaw bot to set up automatic workspace backups to a private GitHub repo.  
> The bot will create the repo, configure the skill, and schedule daily backups.

---

## Prerequisites

1. **GitHub Personal Access Token (PAT)** with `repo` scope  
   → https://github.com/settings/tokens → Generate new token (classic) → check `repo`

2. **OpenClaw self-backup skill installed**  
   → Run: `clawhub install self-backup` (or it may already be in your skills folder)

---

## Prompt

```
Set up automatic self-backup to GitHub. Here's what I want:

1. **Create a private GitHub repo** called `<your-bot-name>-backup` under my account
   - Use this token: ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
   - The repo URL with auth will be: https://ghp_XXX...@github.com/<username>/<repo>.git

2. **Configure the self-backup skill**
   - Edit `skills/openclaw-self-backup/config/backup.json`
   - Enable git backup, point it at the repo above
   - Include: MEMORY.md, memory/*.md, SOUL.md, USER.md, AGENTS.md, IDENTITY.md, TOOLS.md, HEARTBEAT.md, scripts/, config/
   - Retention: 7 daily, 4 weekly, 12 monthly

3. **Store the token in OpenClaw config**
   - Add to env: GITHUB_BACKUP_REPO=https://ghp_XXX...@github.com/<username>/<repo>.git
   - Use: gateway config.patch {"env": {"GITHUB_BACKUP_REPO": "<url>"}}

4. **Create a cron job** for daily backup at 3 AM:
   - sessionTarget: isolated
   - payload: agentTurn — run `bash skills/openclaw-self-backup/scripts/backup.sh`, alert on failure, NO_REPLY on success
   - model: sonnet (or haiku for cost savings)
   - delivery: announce (so failures reach you)

5. **Run a test backup now** to confirm everything works

Report back with:
- ✅ Repo URL
- ✅ Last backup timestamp
- ✅ Cron job ID
```

---

## What Gets Backed Up

| File/Folder | Purpose |
|---|---|
| `MEMORY.md` | Long-term curated memory |
| `memory/YYYY-MM-DD.md` | Daily logs |
| `SOUL.md` | Personality & tone |
| `USER.md` | Human context |
| `AGENTS.md` | Operational guidelines |
| `IDENTITY.md` | Bot identity |
| `TOOLS.md` | Tool notes |
| `HEARTBEAT.md` | Heartbeat checklist |
| `scripts/` | Automation scripts |
| `config/` | Config files |

## Cron Job Template

```json
{
  "name": "GitHub Self-Backup (3 AM)",
  "schedule": { "kind": "cron", "expr": "0 3 * * *", "tz": "Europe/Amsterdam" },
  "sessionTarget": "isolated",
  "payload": {
    "kind": "agentTurn",
    "message": "Run self-backup:\n1. Execute: `bash /path/to/clawd/skills/openclaw-self-backup/scripts/backup.sh`\n2. If non-zero exit: send alert message to user\n3. If success: reply NO_REPLY",
    "model": "anthropic/claude-sonnet-4-6",
    "timeoutSeconds": 180
  },
  "delivery": { "mode": "announce" }
}
```

## Tips

- The PAT only needs `repo` scope — keep it minimal
- Use a dedicated private repo per bot (not shared)
- After a container restart, the GITHUB_BACKUP_REPO env var is available to all scripts
- Test restore occasionally: `bash skills/openclaw-self-backup/scripts/restore.sh --list`
