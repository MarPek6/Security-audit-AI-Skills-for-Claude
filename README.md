# 🔍 Security Auditor — Claude Code Skill

A Claude Code skill that automatically audits GitHub repositories before installation. Inspired by a real security audit of a Claude SEO skill that took hours manually — this skill does it in minutes.

## What it does

When you type something like:
```
audit this skill before I install it: https://github.com/someone/some-skill
```
or
```
is this safe to install? https://github.com/someone/some-skill
```

Claude Code automatically:
1. Clones the repository into a safe sandbox folder
2. Maps the structure (what gets installed where)
3. Reads and analyzes installer scripts
4. Scans Python dependencies (`requirements.txt`)
5. Audits npm dependencies (`package.json`) — including dangerous `postinstall` scripts
6. Audits Docker configuration (`Dockerfile`, `docker-compose.yml`) — including dangerous volume mounts and privileged containers
7. Runs a grep scan for red flag patterns (eval, os.system, credential theft...)
8. Analyzes runtime scripts and hooks
9. Issues a clear verdict: ✅ Safe / ⚠️ Caution / 🚨 Risk

## Supported project types

| Type | Files checked |
|------|--------------|
| Python | `requirements.txt`, `pyproject.toml`, `.py` scripts |
| Node.js / npm | `package.json`, `preinstall`/`postinstall` scripts |
| Docker | `Dockerfile`, `docker-compose.yml`, volume mounts, base images |
| Shell | `install.sh`, `install.ps1`, hooks |

## Supported languages

English, Slovak, Czech, German, French, Italian, Polish, Spanish.

## Installation

```bash
git clone https://github.com/MarPek6/Security-audit-AI-Skills-for-Claude ~/.claude/skills/security-auditor
```

Or manually:
```bash
mkdir -p ~/.claude/skills/security-auditor/references
cp SKILL.md ~/.claude/skills/security-auditor/
cp references/red-flags.md ~/.claude/skills/security-auditor/references/
```

## Usage

In Claude Code (`claude`):

```
audit https://github.com/someone/claude-skill
is this safe to install? https://github.com/user/repo
check this repo before I run install.sh: https://github.com/user/tool
prever toto pred inštaláciou: https://github.com/user/mcp-server
prověř tento skill: https://github.com/user/skill
```

## What gets audited

- `install.sh` / `install.ps1` — installer scripts
- `requirements.txt` / `package.json` — dependencies
- `package.json` scripts — dangerous `preinstall` / `postinstall` hooks
- `Dockerfile` — base image origin, hardcoded secrets, root user
- `docker-compose.yml` — dangerous volume mounts, privileged containers
- Runtime scripts in `scripts/`
- Git hooks in `hooks/`
- Supply chain risk (does REPO_URL point elsewhere?)
- Red flag patterns: eval, exec, os.system, credential theft, system persistence

## Verdict format

```
🔍 Security Audit: repo-name

Overall rating: ✅ Safe

What the installer does: ...
Positive signals: ...
Risk points: ...
Recommendation: You can install via bash install.sh
```

## Why I built this

I installed a Claude SEO skill and went through the entire manual security audit — 18 steps, hours of work. This skill does it automatically in a few minutes.

## License

MIT
