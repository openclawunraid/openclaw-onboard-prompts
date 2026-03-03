# System Audit Prompt

> **Usage:** Paste this into your OpenClaw bot to run a comprehensive system health audit.  
> Works best with a capable model (Opus/Sonnet). Typically takes 5-15 minutes.

---

## Prompt

```
Run a full system audit. Check each section below, report findings with severity (🔴 Critical / 🟠 High / 🟡 Medium / 🟢 OK), apply safe fixes automatically, and ask before doing anything destructive.

## Sections

### 1. Services & Processes
- Are all expected background services running?
- Any zombie processes or stuck jobs?
- Check cron jobs: enabled, last run status, consecutive errors

### 2. Disk & Storage
- Disk usage (warn >80%, critical >90%)
- Large or unexpected files
- Log files that need rotation or cleanup

### 3. Memory & CPU
- Current memory usage
- Any runaway processes consuming excessive CPU/RAM

### 4. Dependencies & Tools
- Are required CLI tools installed? (jq, sqlite3, ffmpeg, etc.)
- Node.js version and npm packages up to date?
- Any missing or broken dependencies

### 5. Credentials & Tokens
- Are API tokens/secrets present in expected locations?
- Any tokens that appear expired or near expiry?
- Config files referencing missing credentials

### 6. Code Quality
- Empty catch blocks (swallowed errors)
- Excessive console.log in production code
- Large files (>500 lines) that could be refactored
- Untested modules

### 7. Test Suite
- Run all available tests
- Report pass/fail count and any regressions
- Flag tests that haven't been updated recently

### 8. Backup & Recovery
- When was the last successful backup?
- Is the backup destination reachable?
- Any backup failures in recent history

### 9. Network & APIs
- Can external APIs be reached? (check any integrations)
- Any rate limit issues or auth failures in logs
- Webhook/callback endpoints responding correctly

### 10. Security Basics
- Are secrets stored in env vars or config, not hardcoded?
- Any world-readable credential files?
- Unnecessary open ports or exposed services

### 11. Logs & Errors
- Recent error patterns in logs
- Any repeated failures that need attention
- Log files growing unexpectedly large

### 12. General Health Score
- Summarize: how many 🔴/🟠/🟡/🟢 findings
- Top 3 things to fix this week
- Any quick wins (safe to auto-fix now)?

## Output Format

End with a summary table:

| Section | Status | Finding |
|---------|--------|---------|
| Services | 🟢 | All running |
| Disk | 🟡 | /data at 78% |
| ... | ... | ... |

**Auto-fixed:** List anything you fixed automatically  
**Needs approval:** List anything that needs human sign-off
```

---

## Tips

- Run weekly (Sunday night works well) via a cron job with an isolated agent
- Use a more powerful model (Opus) for deeper analysis
- Save results to a dated log file for trend tracking across weeks
- Add your own sections for app-specific checks (database integrity, API health, etc.)
