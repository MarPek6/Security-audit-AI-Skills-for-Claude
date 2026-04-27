---
name: security-auditor
description: >
  Audits any GitHub repository for security risks before installation — especially Claude skills, MCP servers, and CLI tools.
  Use this skill whenever the user wants to install, clone, or run anything from GitHub, or asks "is this safe to install?",
  "can I trust this repo?", "audit this skill", "check this before I run it", or pastes a GitHub URL.
  Also triggers for Slovak requests like "prever toto repo", "je to bezpečné nainštalovať?", "skontroluj skill pred inštaláciou".
  Also triggers for Czech requests like "prověř toto repo", "je to bezpečné nainstalovat?", "zkontroluj skill před instalací", "ověř tento repozitář".
  Also triggers for German requests like "überprüfe dieses Repo", "ist das sicher zu installieren?", "prüfe diesen Skill vor der Installation".
  Also triggers for French requests like "vérifie ce dépôt", "est-ce sûr à installer?", "audite ce skill avant installation".
  Also triggers for Italian requests like "controlla questo repo", "è sicuro da installare?", "verifica questo skill prima dell'installazione".
  Also triggers for Polish requests like "sprawdź to repo", "czy to jest bezpieczne do zainstalowania?", "zweryfikuj ten skill przed instalacją".
  Also triggers for Spanish requests like "revisa este repositorio", "¿es seguro instalarlo?", "audita este skill antes de instalarlo".
  Always use this skill before any installation — it finds malware patterns, supply chain risks, and sketchy behavior in installers.
---

# Security Auditor

> **EN:** Systematically audits GitHub repositories before installation. Always works step by step, explains what it does and why, and delivers a clear verdict.
>
> **SK:** Systematicky audituje GitHub repozitáre pred inštaláciou. Vždy pracuje krok za krokom, vysvetľuje čo robí a prečo, a dáva jasný verdikt.

---

## Workflow

### Step 1 — Receive URL and prepare / Krok 1 — Príjem URL a príprava

> **EN:** When you receive a GitHub URL, clone the repository into a safe sandbox folder on the Desktop. Never run install scripts — only read them.
>
> **SK:** Keď dostaneš GitHub URL, klonuj repozitár do bezpečného sandbox priečinka na Ploche. Nikdy nespúšťaj install skripty — len ich čítaj.

```bash
mkdir -p ~/Desktop/security-audit-<repo-name>
git clone <url> ~/Desktop/security-audit-<repo-name>
cd ~/Desktop/security-audit-<repo-name>
```

---

### Step 2 — Map the repository / Krok 2 — Mapovanie repozitára

> **EN:** List all files to understand what the repository contains before touching anything.
>
> **SK:** Vypíš všetky súbory aby si pochopil čo repozitár obsahuje skôr než sa čohokoľvek dotkneš.

```bash
find . -maxdepth 3 -type f | sort
```

Look for / Hľadaj:
- `install.sh`, `install.ps1` — **EN:** main entry points / **SK:** hlavné vstupné body
- `requirements.txt`, `package.json`, `pyproject.toml` — **EN:** dependencies / **SK:** závislosti
- `Dockerfile`, `docker-compose.yml` — **EN:** Docker configuration / **SK:** Docker konfigurácia
- `hooks/`, `.github/workflows/` — **EN:** automatically triggered scripts / **SK:** automaticky spúšťané skripty
- `uninstall.sh` — **EN:** how to remove it / **SK:** ako sa to dá odstrániť

---

### Step 3 — Analyze the installer script / Krok 3 — Analýza installer skriptu

> **EN:** Read the installer carefully. Do not run it. Look for where it writes files, what it downloads, and whether it points to itself or a different repository.
>
> **SK:** Čítaj installer pozorne. Nespúšťaj ho. Hľadaj kam zapisuje súbory, čo sťahuje, a či ukazuje na seba alebo iný repozitár.

```bash
cat install.sh
```

Check / Kontroluj:
- `REPO_URL` — **EN:** does it point to itself or a different repo? (supply chain risk) / **SK:** ukazuje na seba alebo na iné repo? (supply chain riziko)
- `REPO_TAG` / `--branch` — **EN:** is the version pinned? (safer than main) / **SK:** je verzia pinovaná? (lepšie ako main)
- **EN:** Where does it write? `~/.claude`, `~/.local`, `/usr/local` / **SK:** Kam zapisuje?
- `curl | bash` — **EN:** red flag if used inside the installer itself / **SK:** červená vlajka ak sa používa vnútri installera
- `trap "rm -rf ..."` — **EN:** temporary folders are OK, anything else is not / **SK:** dočasné priečinky OK, inak nie
- `chmod +x` — **EN:** acceptable for shell/python scripts / **SK:** akceptovateľné pre shell/python skripty

---

### Step 4 — Analyze dependencies / Krok 4 — Analýza závislostí

> **EN:** Check what external packages the project installs. Unknown or unversioned packages are a supply chain risk.
>
> **SK:** Skontroluj aké externé balíky projekt inštaluje. Neznáme alebo neverziované balíky sú supply chain riziko.

```bash
cat requirements.txt
# or / alebo
cat package.json
```

Look for / Hľadaj:
- **EN:** Unknown or exotic packages not found on PyPI mainstream / **SK:** Neznáme alebo exotické balíky mimo PyPI mainstream
- **EN:** Packages that steal credentials (keyloggers, clipboard stealers) / **SK:** Balíky ktoré kradnú credentials
- **EN:** Dependencies without version pinning (supply chain risk) / **SK:** Závislosti bez verzie (supply chain riziko)

**EN:** Safe packages: `requests`, `beautifulsoup4`, `playwright`, `PIL`, `matplotlib`, `weasyprint`, `google-api-python-client`
**SK:** Bezpečné balíky: `requests`, `beautifulsoup4`, `playwright`, `PIL`, `matplotlib`, `weasyprint`, `google-api-python-client`

---

### Step 5 — Red flag grep scan / Krok 5 — Red flag grep sken

> **EN:** Scan the entire repository for dangerous patterns. Evaluate every result in context — not every match is a problem.
>
> **SK:** Skenuj celý repozitár na nebezpečné vzory. Hodnoť každý výsledok v kontexte — nie každý nález je problém.

```bash
grep -RniE "curl|wget|nc |netcat|bash -c|sh -c|eval\(|os\.system|subprocess|rm -rf|launchctl|defaults write|security find|id_rsa|aws_access_key|api[_-]?key|token|process\.env|base64|exec\(" . 2>/dev/null | grep -v ".git/" | grep -v Binary
```

Evaluate each result / Hodnoť každý nález:
- **Documentation / README** — **EN:** ignore, these are just examples / **SK:** ignoruj, len príklady
- **Uninstall script** — `rm -rf ~/.tool/` **EN:** is OK / **SK:** je OK
- **Runtime scripts** — **EN:** SSRF protection? Where does it send data? / **SK:** SSRF ochrana? Kam posiela dáta?
- `eval()`, `os.system()` — **EN:** red flag if used with dynamic/external input / **SK:** červená vlajka ak s dynamickými vstupmi

---

### Step 6 — Analyze runtime scripts / Krok 6 — Analýza runtime skriptov

> **EN:** Read every script in the `scripts/` folder. Look for network calls, credential access, and dangerous functions.
>
> **SK:** Prečítaj každý skript v priečinku `scripts/`. Hľadaj sieťové volania, prístup ku credentials, a nebezpečné funkcie.

```bash
cat scripts/<script>.py
```

Look for / Hľadaj:
- **EN:** SSRF protection — does it block private IPs? (`ip.is_private`) / **SK:** SSRF ochrana — blokuje private IP?
- **EN:** Where does it make HTTP requests? (only to declared services?) / **SK:** Kam robí HTTP requesty?
- **EN:** Does it read tokens/keys/env variables and where does it send them? / **SK:** Číta tokeny/kľúče/env premenné a kam ich posiela?
- `eval()`, `exec()`, `os.system()` — **EN:** red flag with dynamic inputs / **SK:** červená vlajka s dynamickými vstupmi

---

### Step 7 — npm audit (if package.json exists) / Krok 7 — npm audit (ak existuje package.json)

> **EN:** JavaScript/Node.js projects use npm for dependencies. The biggest risk is `preinstall`/`postinstall` scripts that run automatically during `npm install` without asking.
>
> **SK:** JavaScript/Node.js projekty používajú npm pre závislosti. Najväčším rizikom sú `preinstall`/`postinstall` skripty ktoré sa spúšťajú automaticky počas `npm install` bez pýtania.

```bash
cat package.json
cat package.json | grep -A5 '"scripts"'
```

**EN:** Red flags in npm / **SK:** Červené vlajky v npm:
- `"preinstall": "curl ... | bash"` — **EN:** downloads and runs code during installation / **SK:** stiahne a spustí kód pri inštalácii
- `"postinstall": "node steal-keys.js"` — **EN:** runs a script after installation / **SK:** spustí skript po inštalácii
- `"lodash": "*"` — **EN:** no version pinned, always installs latest, unpredictable / **SK:** bez verzie, vždy najnovšia, nepredvídateľné
- **EN:** Unknown package using `child_process` — can execute system commands / **SK:** Neznámy balík s `child_process` — môže spúšťať systémové príkazy

**EN:** Safe npm packages: `express`, `axios`, `lodash`, `react`, `typescript`, `eslint`, `prettier`, `dotenv`
**SK:** Bezpečné npm balíky: `express`, `axios`, `lodash`, `react`, `typescript`, `eslint`, `prettier`, `dotenv`

---

### Step 8 — Docker audit (if Dockerfile or docker-compose.yml exists) / Krok 8 — Docker audit (ak existuje Dockerfile alebo docker-compose.yml)

> **EN:** Docker packages an entire application into a container. The risks are different from scripts — dangerous volume mounts give containers access to your files, and unknown base images can contain anything.
>
> **SK:** Docker zabalí celú aplikáciu do kontajnera. Riziká sú iné ako pri skriptoch — nebezpečné volume mounty dávajú kontajnerom prístup k tvojim súborom, a neznáme base images môžu obsahovať čokoľvek.

```bash
cat Dockerfile
cat docker-compose.yml
```

**EN:** In Dockerfile look for / **SK:** V Dockerfile hľadaj:
- `FROM someuser/unknown-image` — **EN:** always prefer official images with specific version tags / **SK:** vždy preferuj oficiálne images s konkrétnou verziou
- `RUN curl ... | bash` — **EN:** same red flag as in shell scripts / **SK:** rovnaká červená vlajka ako v shell skriptoch
- `ENV API_KEY=abc123` — **EN:** hardcoded secrets visible to anyone who pulls the image / **SK:** hardcoded secrets viditeľné každému kto image stiahne
- `USER root` — **EN:** without later switching to a non-root user / **SK:** bez neskoršieho prepnutia na non-root užívateľa

**EN:** In docker-compose.yml look for / **SK:** V docker-compose.yml hľadaj:
- `- /:/host` alebo `- ~/.ssh:/root/.ssh` — **EN:** mounting SSH keys or entire filesystem into container is critical risk / **SK:** mount SSH kľúčov alebo celého filesystému je kritické riziko
- `privileged: true` — **EN:** container has full access to host system, bypasses all Docker security / **SK:** kontajner má plný prístup k hostiteľskému systému
- `network_mode: host` — **EN:** container shares network with host / **SK:** kontajner zdieľa sieť s hostiteľom
- `image: someuser/tool:latest` — **EN:** no specific version, unpredictable / **SK:** bez konkrétnej verzie, nepredvídateľné

```bash
# Check for hardcoded secrets / Skontroluj hardcoded secrets
grep -rniE "password|secret|api_key|token|private_key" Dockerfile docker-compose.yml 2>/dev/null
```

**EN:** Safe Docker patterns: `FROM node:20-alpine`, `FROM python:3.11-slim`, `USER node`
**SK:** Bezpečné Docker vzory: `FROM node:20-alpine`, `FROM python:3.11-slim`, `USER node`

---

### Step 9 — Analyze hooks / Krok 9 — Analýza hookov

> **EN:** Hooks run automatically in certain situations (git commits, installations). Check what they do and whether they make network calls.
>
> **SK:** Hooky sa spúšťajú automaticky v určitých situáciách (git commity, inštalácie). Skontroluj čo robia a či robia sieťové volania.

```bash
find . -path "*/hooks/*" -type f | sort
cat hooks/*.sh
cat hooks/*.py
```

Look for / Hľadaj:
- **EN:** Automatic execution without user consent / **SK:** Automatické spúšťanie bez súhlasu
- **EN:** Network calls / **SK:** Sieťové volania
- **EN:** Deleting or modifying files outside the installation scope / **SK:** Mazanie alebo modifikácia súborov mimo scope inštalácie

---

### Step 10 — Verdict / Krok 10 — Verdikt

> **EN:** Issue a clear verdict in the user's language using this structure.
>
> **SK:** Vydaj jasný verdikt v jazyku užívateľa v tejto štruktúre.

---
**🔍 Security Audit: [repo-name]**

**Overall rating / Celkové hodnotenie:** ✅ Safe / Bezpečné — ⚠️ Caution / Opatrnosť — 🚨 Risk / Riziko

**What the installer does / Čo robí installer:**
- [list of steps / zoznam krokov]

**Positive signals / Pozitívne signály:**
- [list / zoznam]

**Risk points / Rizikové body:**
- [list with explanation / zoznam s vysvetlením]

**Recommendation / Odporúčanie:**
- You can install via `bash install.sh` / Môžeš inštalovať — Install manually only / Inštaluj len ručne — Do not install / Neinštaluj

**If you install / Ak inštaluješ:**
- [specific safe installation steps / konkrétne kroky pre bezpečnú inštaláciu]
---

---

## Evaluation patterns / Vzory hodnotenia

### ✅ Safe signals / Bezpečné signály

- **EN:** SSRF protection in runtime scripts / **SK:** SSRF ochrana v runtime skriptoch
- **EN:** REPO_URL points to itself, not a fork / **SK:** REPO_URL ukazuje na seba, nie fork
- **EN:** Version pinned to a tag, not `main` / **SK:** Verzia pinovaná na tag, nie `main`
- **EN:** Tokens stored locally, not sent to author / **SK:** Tokeny ukladané lokálne, nie odosielané autorovi
- **EN:** OAuth callback on `localhost` / **SK:** OAuth callback na `localhost`
- **EN:** Python `venv` for isolated dependencies / **SK:** Python `venv` pre izolované závislosti
- **EN:** Readable, non-obfuscated code / **SK:** Čitateľný, neobfuskovaný kód
- **EN:** Official Docker base image with specific version / **SK:** Oficiálny Docker base image s konkrétnou verziou

### ⚠️ Caution / Opatrnosť

- **EN:** REPO_URL points to a different repository / **SK:** REPO_URL ukazuje na iné repo
- **EN:** curl one-liner in documentation / **SK:** Curl one-liner v dokumentácii
- **EN:** Many files copied to system directories / **SK:** Veľa súborov kopírovaných do systémových priečinkov
- **EN:** External API integrations without clear documentation / **SK:** Externé API integrácie bez jasnej dokumentácie
- **EN:** npm packages without version pinning / **SK:** npm balíky bez pinovanej verzie

### 🚨 Red flags / Červené vlajky

- `eval(user_input)` or `exec()` — **EN:** with external/dynamic data / **SK:** s externými/dynamickými dátami
- **EN:** Reading `~/.ssh/id_rsa`, AWS keys, env variables and sending them out / **SK:** Čítanie SSH kľúčov, AWS kľúčov a odosielanie von
- `curl | bash` — **EN:** inside the installer itself / **SK:** vnútri install skriptu
- **EN:** Obfuscated code (base64 encoded commands) / **SK:** Obfuskovaný kód
- `launchctl` / `defaults write` — **EN:** system persistence after restart / **SK:** systémová perzistencia po reštarte
- **EN:** Docker: `privileged: true` or SSH volume mount / **SK:** Docker: `privileged: true` alebo SSH volume mount
- **EN:** npm: malicious `postinstall` script / **SK:** npm: škodlivý `postinstall` skript

---

## Safe installation (if audit passes) / Bezpečná inštalácia (ak je audit OK)

> **EN:** Always recommend taking a snapshot before and after installation so you can see exactly what changed.
>
> **SK:** Vždy odporúčaj urobiť snapshot pred a po inštalácii aby si videl čo presne sa zmenilo.

```bash
# 1. Snapshot before / Snapshot pred inštaláciou
ls -la ~/.claude > /tmp/before.txt

# 2. Run local installer (never curl | bash) / Spusti lokálny installer (nie curl | bash)
bash install.sh

# 3. See what changed / Pozri čo sa zmenilo
find ~/.claude -maxdepth 3 -type f | sort > /tmp/after.txt
diff /tmp/before.txt /tmp/after.txt
```

---

## Communication rules / Pravidlá komunikácie

> **EN:** Always explain each step in simple terms — what you are doing, why, what will happen, and what the next step is. Never run the installer without explicit user consent. Always ask before any action that modifies the system.
>
> **SK:** Vždy vysvetľuj každý krok jednoducho — čo robíš, prečo, čo sa stane, a čo je ďalší krok. Nikdy nespúšťaj installer bez explicitného súhlasu užívateľa. Vždy sa opýtaj pred akoukoľvek akciou ktorá mení systém.

---

## Reference files / Referenčné súbory

- `references/red-flags.md` — **EN:** extended database of red flag patterns / **SK:** rozšírená databáza red flag patternov
