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

Systematicky audituje GitHub repozitáre pred inštaláciou. Vždy pracuje krok za krokom, vysvetľuje čo robí a prečo, a dáva jasný verdikt.

## Workflow

### Krok 1 — Príjem URL a príprava

Keď dostaneš GitHub URL:
1. Vytvor bezpečný pracovný priečinok: `mkdir -p ~/Desktop/security-audit-<repo-name>`
2. Klonuj repo bez spúšťania čohokoľvek: `git clone <url> ~/Desktop/security-audit-<repo-name>`
3. Nikdy nespúšťaj install skripty — len ich čítaj.

### Krok 2 — Mapovanie repozitára

```bash
find . -maxdepth 3 -type f | sort
```

Hľadaj:
- `install.sh`, `install.ps1` — hlavné vstupné body
- `requirements.txt`, `package.json`, `pyproject.toml` — závislosti
- `hooks/`, `.github/workflows/` — automaticky spúšťané veci
- `uninstall.sh` — ako sa to dá odstrániť

### Krok 3 — Analýza installer skriptu

```bash
cat install.sh
```

Kontroluj:
- `REPO_URL` — ukazuje na seba alebo na iné repo? (supply chain riziko)
- `REPO_TAG` / `--branch` — je verzia pinovaná? (lepšie ako main)
- Kam zapisuje: `~/.claude`, `~/.local`, `/usr/local`?
- Či spúšťa `curl | bash` vnútri seba (red flag)
- `trap "rm -rf ..."` — dočasné priečinky OK, inak nie
- `chmod +x` — akceptovateľné pre shell/python skripty

### Krok 4 — Analýza závislostí

```bash
cat requirements.txt
# alebo
cat package.json
```

Hľadaj:
- Neznáme alebo exotické balíky (nie PyPI mainstream)
- Balíky ktoré kradnú credentials (keyloggers, clipboard stealers)
- Závislosti bez verzie (supply chain riziko)

Bezpečné kategórie: requests, beautifulsoup4, playwright, PIL, matplotlib, weasyprint, google-api-python-client

### Krok 5 — Red flag grep (celé repo)

```bash
grep -RniE "curl|wget|nc |netcat|bash -c|sh -c|eval\(|os\.system|subprocess|rm -rf|launchctl|defaults write|security find|id_rsa|aws_access_key|api[_-]?key|token|process\.env|base64|exec\(" . 2>/dev/null | grep -v ".git/" | grep -v Binary
```

Hodnoť každý nález:
- **Dokumentácia / README** — ignoruj (len príklady)
- **Uninstall skript** — `rm -rf ~/.tool/` je OK
- **Runtime skripty** — SSRF ochrana? Kam posiela dáta?
- **eval(, os.system, subprocess.call** — červená vlajka ak hardcoded príkazy

### Krok 6 — Analýza runtime skriptov

Pre každý `.py` alebo `.sh` skript v `scripts/`:
```bash
cat scripts/<script>.py
```

Hľadaj:
- SSRF ochranu (blokuje private IP? `ip.is_private`)
- Kam robí HTTP requesty (len na deklarované služby?)
- Či číta tokeny / kľúče / env premenné a kam ich posiela
- `eval()`, `exec()`, `os.system()` s dynamickými vstupmi

### Krok 7 — Analýza hookov

```bash
find . -path "*/hooks/*" -type f | sort
cat hooks/*.sh
cat hooks/*.py
```

Hľadaj:
- Automatické spúšťanie bez súhlasu
- Sieťové volania
- Mazanie alebo modifikácia súborov mimo scope

### Krok 8 — Verdikt

Vydaj jasný verdikt v tejto štruktúre:

---
**🔍 Security Audit: [repo-name]**

**Celkové hodnotenie:** ✅ Bezpečné / ⚠️ Opatrnosť / 🚨 Riziko

**Čo robí installer:**
- [zoznam krokov]

**Pozitívne signály:**
- [zoznam]

**Rizikové body:**
- [zoznam s vysvetlením]

**Odporúčanie:**
- Môžeš inštalovať cez `bash install.sh` / Inštaluj len ručne / Neinštaluj

**Ak inštaluješ:**
- [konkrétne kroky pre bezpečnú inštaláciu]
---

## Vzory hodnotenia

### ✅ Bezpečné signály
- SSRF ochrana v runtime skriptoch
- REPO_URL ukazuje na seba (nie fork)
- Verzia pinovaná na tag (nie `main`)
- Tokeny ukladané lokálne, nie odosielané autorovi
- OAuth callback na `localhost`
- `venv` pre Python závislosti
- Čitateľný, neobfuskovaný kód

### ⚠️ Opatrnosť
- REPO_URL ukazuje na iné repo
- Curl one-liner v dokumentácii
- Veľa súborov kopírovaných do systémových priečinkov
- Externé API integrácie bez jasnej dokumentácie

### 🚨 Červené vlajky
- `eval(user_input)` alebo `exec()` s externými dátami
- Čítanie `~/.ssh/id_rsa`, AWS kľúčov, env premenných a odosielanie von
- `curl | bash` vnútri install skriptu
- Obfuskovaný kód (base64 encoded commands)
- `launchctl` alebo `defaults write` (systémová perzistencia)
- Zmazanie súborov mimo scope inštalácie

## Bezpečná inštalácia (ak je audit OK)

Vždy odporúčaj ručnú inštaláciu pred `bash install.sh`:

```bash
# 1. Snapshot pred inštaláciou
ls -la ~/.claude > /tmp/before.txt

# 2. Spusti lokálny installer (nie curl | bash)
bash install.sh

# 3. Pozri čo sa zmenilo
find ~/.claude -maxdepth 3 -type f | sort > /tmp/after.txt
diff /tmp/before.txt /tmp/after.txt
```

## Komunikácia

Vysvetľuj každý krok jednoducho:
- Čo robíš a prečo
- Čo sa stane (a čo sa NEStane)
- Čo si práve urobil
- Čo je ďalší krok

Nikdy nespúšťaj installer bez explicitného súhlasu používateľa. Vždy sa opýtaj pred akoukoľvek akciou ktorá mení systém.

## Referenčné súbory

- `references/red-flags.md` — rozšírený zoznam red flag patternov
- `references/safe-patterns.md` — príklady bezpečného kódu
