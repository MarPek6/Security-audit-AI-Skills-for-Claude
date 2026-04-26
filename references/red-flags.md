Red Flag Patterns — Security Auditor Reference
Critical / Kritické / Kritické / Kritisch / Critique / Critico / Krytyczne / Crítico
Data exfiltration / Exfiltrácia dát / Exfiltrace dat / Datenexfiltration / Exfiltration de données / Esfiltrazione dati / Eksfiltracja danych / Exfiltración de datos
Reading SSH keys, env variables or macOS Keychain and sending them to an external server.
Čítanie SSH kľúčov, env premenných alebo macOS Keychain a odosielanie na externý server.
Čtení SSH klíčů, env proměnných nebo macOS Keychain a odesílání na externí server.
Lesen von SSH-Schlüsseln, Umgebungsvariablen oder macOS Keychain und Senden an externen Server.
Lecture des clés SSH, variables d'environnement ou macOS Keychain et envoi à un serveur externe.
Lettura di chiavi SSH, variabili d'ambiente o macOS Keychain e invio a server esterno.
Odczyt kluczy SSH, zmiennych środowiskowych lub macOS Keychain i wysyłanie na zewnętrzny serwer.
Lectura de claves SSH, variables de entorno o macOS Keychain y envío a servidor externo.
System persistence / Systémová perzistencia / Systémová perzistence / Systembeständigkeit / Persistance système / Persistenza di sistema / Trwałość systemu / Persistencia del sistema
launchctl load or defaults write — runs malicious code after every restart.
launchctl load alebo defaults write — spúšťa škodlivý kód po každom reštarte.
launchctl load nebo defaults write — spouští škodlivý kód po každém restartu.
launchctl load oder defaults write — führt Schadcode nach jedem Neustart aus.
launchctl load ou defaults write — exécute du code malveillant après chaque redémarrage.
launchctl load o defaults write — esegue codice dannoso dopo ogni riavvio.
launchctl load lub defaults write — uruchamia złośliwy kod po każdym restarcie.
launchctl load o defaults write — ejecuta código malicioso tras cada reinicio.
Obfuscation / Obfuskácia / Obfuskace / Verschleierung / Obfuscation / Offuscamento / Obfuskacja / Ofuscación
exec(base64.b64decode(...)) or eval(requests.get(...).text) — downloads and runs hidden code.
exec(base64.b64decode(...)) alebo eval(requests.get(...).text) — stiahne a spustí skrytý kód.
exec(base64.b64decode(...)) nebo eval(requests.get(...).text) — stáhne a spustí skrytý kód.
exec(base64.b64decode(...)) oder eval(requests.get(...).text) — lädt versteckten Code herunter und führt ihn aus.
exec(base64.b64decode(...)) ou eval(requests.get(...).text) — télécharge et exécute du code caché.
exec(base64.b64decode(...)) o eval(requests.get(...).text) — scarica ed esegue codice nascosto.
exec(base64.b64decode(...)) lub eval(requests.get(...).text) — pobiera i uruchamia ukryty kod.
exec(base64.b64decode(...)) o eval(requests.get(...).text) — descarga y ejecuta código oculto.
Supply chain risk / Supply chain riziko / Supply chain riziko / Lieferkettenrisiko / Risque chaîne d'approvisionnement / Rischio supply chain / Ryzyko łańcucha dostaw / Riesgo cadena de suministro
REPO_URL points to a different repository than the one you audited.
REPO_URL ukazuje na iný repozitár než ten, ktorý si auditoval.
REPO_URL ukazuje na jiný repozitář než ten, který jsi auditoval.
REPO_URL verweist auf ein anderes Repository als das geprüfte.
REPO_URL pointe vers un dépôt différent de celui audité.
REPO_URL punta a un repository diverso da quello verificato.
REPO_URL wskazuje na inne repozytorium niż to, które sprawdzałeś.
REPO_URL apunta a un repositorio diferente al auditado.
Medium risk / Stredné riziko / Střední riziko / Mittleres Risiko / Risque moyen / Rischio medio / Średnie ryzyko / Riesgo medio
Writing to system directories
Copying to /usr/local/bin/ or /etc/ without a clear reason.
Kopírovanie do /usr/local/bin/ alebo /etc/ bez jasného dôvodu.
Kopírování do /usr/local/bin/ nebo /etc/ bez jasného důvodu.
Kopieren nach /usr/local/bin/ oder /etc/ ohne klaren Grund.
Copie dans /usr/local/bin/ ou /etc/ sans raison claire.
Copia in /usr/local/bin/ o /etc/ senza motivo chiaro.
Kopiowanie do /usr/local/bin/ lub /etc/ bez wyraźnego powodu.
Copia en /usr/local/bin/ o /etc/ sin razón clara.
Ping home
Sending installation data to the author without documentation.
Odosielanie inštalačných dát autorovi bez dokumentácie.
Odesílání instalačních dat autorovi bez dokumentace.
Senden von Installationsdaten an den Autor ohne Dokumentation.
Envoi de données d'installation à l'auteur sans documentation.
Invio di dati di installazione all'autore senza documentazione.
Wysyłanie danych instalacyjnych do autora bez dokumentacji.
Envío de datos de instalación al autor sin documentación.
Acceptable patterns / Akceptovateľné vzory / Přijatelné vzory / Akzeptable Muster / Modèles acceptables / Pattern accettabili / Akceptowalne wzorce / Patrones aceptables
Temporary directories
trap "rm -rf ${TEMP_DIR}" EXIT — cleanup after installation, normal behavior.
Python venv
python3 -m venv .venv — isolated environment, safe.
SSRF protection
Blocking private IP addresses in runtime scripts — positive security signal.
OAuth on localhost
OAUTH_REDIRECT_URI = "http://localhost:8085" — standard safe OAuth pattern.
Pinned version
git clone --branch "v1.6.0" — not live main, safer.
