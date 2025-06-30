# Bauanleitung für Windows 64

## Installation der erforderlichen Entwicklungswerkzeuge

Dies wird für das Kompilieren jedes Viewers benötigt, der auf dem Linden Lab Open-Source-Code basiert, und muss nur einmal durchgeführt werden.

Alle Installationen werden mit Standardeinstellungen durchgeführt (sofern nicht anders angegeben) – wenn du diese änderst, bist du auf dich allein gestellt!

### Windows

- Installiere Windows 10/11 64-Bit mit deinem eigenen Produktschlüssel

### Microsoft Visual Studio 2022

- Installiere Visual Studio 2022
  - Führe das Installationsprogramm als Administrator aus (Rechtsklick, "Als Administrator ausführen")
  - Aktiviere "Desktopentwicklung mit C++" im Reiter "Workloads".
  - Alle anderen Workload-Optionen können deaktiviert bleiben

> [!TIP]
> Falls du keine kommerzielle Version von Visual Studio 2022 (z.B. Professional) besitzt, kannst du die [Community-Version](https://visualstudio.microsoft.com/free-developer-offers) installieren.

### Tortoise Git

- Lade [TortoiseGit 2.9.0 oder neuer](https://tortoisegit.org) (64-Bit) herunter und installiere es
  - Hinweis: Es gibt keine Option, es als Administrator zu installieren
  - Verwende die Standardoptionen (Pfad, Komponenten etc.) für Tortoise Git selbst
  - An einem bestimmten Punkt wird es dich auffordern, Git für Windows herunterzuladen und zu installieren
    - Hier kannst du die Standardoptionen verwenden, **AUSSER** wenn es nach der "Konfiguration der Zeilenenden-Umwandlung" fragt: Hier **MUSST** du "Checkout as-is, commit as-is" auswählen!

### CMake

- Lade mindestens [CMake 3.16.0](http://www.cmake.org/download) herunter und installiere es
  - Hinweis: Es gibt keine Option, es als Administrator zu installieren
  - Wähle im Bildschirm "Installationsoptionen" die Option "Add CMake to the system PATH for all users"
  - Für alles andere verwende die Standardoptionen (Pfad etc.)
  - Stelle sicher, dass folgendes Verzeichnis zu deinem Pfad hinzugefügt wurde:
    Für die 32-Bit-Version:
    `C:\Program Files (x86)\CMake\bin`
    Für die 64-Bit-Version:
    `C:\Program Files\CMake\bin`

### Cygwin

- Lade [Cygwin 64](http://cygwin.com/install.html) (64-Bit) herunter und installiere es
  - Führe das Installationsprogramm als Administrator aus (Rechtsklick, "Als Administrator ausführen")
  - Verwende Standardoptionen (Pfad, Komponenten etc.), **bis** du zum Bildschirm "Pakete auswählen" kommst
  - Füge zusätzliche Pakete hinzu:
    - Devel/patch
  - Verwende Standardoptionen für alles andere
  - Stelle sicher, dass folgendes Verzeichnis zu deinem Pfad hinzugefügt wurde und dass es vor `%SystemRoot%\system32` steht:
    `C:\Cygwin64\bin`

### Python

- Lade die neueste Version von [Python 3](https://www.python.org/downloads/windows) herunter und installiere sie
  - Führe das Installationsprogramm als Administrator aus (Rechtsklick, "Als Administrator ausführen")
  - Aktiviere "Add Python 3.10 to PATH"
  - Wähle die Option "Benutzerdefinierte Installation".
    - Stelle sicher, dass "pip" aktiviert ist.
    - "Dokumentation", "tcl/tk und IDLE", "Python-Testsuite" und "py launcher" werden nicht zum Kompilieren des Viewers benötigt, können aber ausgewählt werden, falls gewünscht.
    - Im nächsten Bildschirm sollten bereits die korrekten Optionen aktiviert sein.
    - Setze den benutzerdefinierten Installationspfad auf: `C:\Python3`
  - Stelle sicher, dass folgendes Verzeichnis zu deinem Pfad hinzugefügt wurde: `C:\Python3`

> [!TIP]
> Unter Windows 10/11 solltest du möglicherweise auch den App-Alias für Python deaktivieren. Öffne die Windows-Einstellungen, suche nach "App-Ausführungsaliase verwalten" und deaktiviere den Alias für "python3.exe".

### Zwischenprüfung

Überprüfe, ob alles korrekt installiert wurde, indem du ein Cygwin-Terminal öffnest und folgende Befehle eingibst:

```
cmake --version
git --version
python --version
pip --version
```

Wenn alle sinnvolle Werte anzeigen und keine "Command not found"-Fehler auftreten, bist du auf dem richtigen Weg.

> [!NOTE]
> Das Cygwin-Terminal wird nur für Tests benötigt. Alle Befehle zum eigentlichen Bau des Viewers werden in der Windows-Eingabeaufforderung ausgeführt.

### Autobuild einrichten

- Installiere Autobuild
  Du kannst Autobuild und seine Abhängigkeiten mit der `requirements.txt`-Datei installieren, die Teil des Repositories ist. Dadurch wird mit denselben Versionen gebaut, die auch für die offiziellen Builds verwendet werden.
  - Öffne die Windows-Eingabeaufforderung und gib ein:  
    `pip install -r requirements.txt`
  - Autobuild wird installiert. **Ältere Versionen von Autobuild konnten durch einfaches Einfügen der Quelldateien in den Pfad zum Laufen gebracht werden; das ist nicht mehr der Fall – Autobuild _muss_ wie hier beschrieben installiert werden.**
  - Öffne die Windows-Eingabeaufforderung und gib ein:  
    `pip install git+https://github.com/secondlife/autobuild.git#egg=autobuild`
- Setze die Umgebungsvariable AUTOBUILD_VSVER auf 170 (170 = Visual Studio 2022).
- Überprüfe die Autobuild-Version auf "autobuild 3.8" oder höher:  
  `autobuild --version`

### NSIS

- Falls du den Viewer packen und eine Installationsdatei erstellen möchtest, musst du NSIS von der [offiziellen Website](https://nsis.sourceforge.io) installieren.
- Nicht erforderlich, es sei denn, du möchtest einen tatsächlichen Viewer-Installer für die Verteilung erstellen oder die NSIS-Installer-Logik selbst ändern.

> [!IMPORTANT]
> Falls du den Viewer auf einer Revision vor dem [Bugsplat-Merge](https://github.com/FirestormViewer/phoenix-firestorm/commit/a399c6778579ac7c8965737088c275dde1371c9e) packen möchtest, musst du die Unicode-Version von NSIS [von hier](http://www.scratchpaper.com) installieren – der Installer von der NSIS-Website wird **NICHT** funktionieren!

## Viewer-Build-Variablen einrichten

Um das Bauen von zusammengehörigen Paketen (wie dem Viewer und allen Bibliotheken, die er importiert) mit denselben Kompilierungsoptionen zu vereinfachen, erwartet Autobuild eine Datei mit Variablendefinitionen. Diese kann über die Umgebungsvariable AUTOBUILD_VARIABLES_FILE festgelegt werden.

- Klone das Build-Variablen-Repository:  
  `git clone https://github.com/FirestormViewer/fs-build-variables.git <Pfad-zur-Variablendatei>`
- Setze die Umgebungsvariable AUTOBUILD_VARIABLES_FILE auf  
  `<Pfad-zur-Variablendatei>\variables`

## Visual Studio 2022 konfigurieren (optional)

- Starte die IDE
- Navigiere zu **Tools** > **Options** > **Projects and Solutions** > **Build and Run** und setze **maximum number of parallel projects builds** auf **1**.

## Quellcode-Verzeichnis einrichten

Plane deine Verzeichnisstruktur im Voraus. Falls du Änderungen oder Patches erstellen möchtest, wirst du für jede Änderung oder jeden Patch eine unveränderte Version des Quellcodes klonen müssen. Daher solltest du alle Arbeiten in einem eigenen Verzeichnis speichern. Falls du nur gelegentlich kompilierst und keine Änderungen vornehmen möchtest, reicht ein Verzeichnis aus. Für dieses Dokument wird angenommen, dass du einen Ordner `c:\firestorm` erstellt hast.

```
c:
cd \firestorm
git clone https://github.com/FirestormViewer/phoenix-firestorm.git
```

## Drittanbieter-Bibliotheken vorbereiten

Die meisten Drittanbieter-Bibliotheken, die zum Bau des Viewers benötigt werden, werden automatisch heruntergeladen und während der Kompilierung in das Build-Verzeichnis deines Quellcode-Baums installiert. Einige müssen manuell vorbereitet werden und sind normalerweise nicht erforderlich, wenn eine Open-Source-Konfiguration (ReleaseFS_open) verwendet wird.

> [!IMPORTANT]
> Falls du die Drittanbieter-Bibliotheken manuell baust, musst du die korrekte Version erstellen (32-Bit-Bibliotheken für einen 32-Bit-Viewer, 64-Bit-Versionen für einen 64-Bit-Viewer)!

## FMOD Studio mit Autobuild

Falls du FMOD Studio verwenden möchtest, um Sounds im Viewer abzuspielen, musst du eine eigene Kopie herunterladen. FMOD Studio kann [hier](https://www.fmod.com) heruntergeladen werden (erfordert die Erstellung eines Kontos für den Download-Bereich).

> [!IMPORTANT]
> Stelle sicher, dass du die FMOD Studio API und nicht das FMOD Studio Tool herunterlädst!

```
c:
cd \firestorm
git clone https://github.com/FirestormViewer/3p-fmodstudio.git
```

- Nachdem du das Repository geklont hast, kopiere die heruntergeladene FMOD Studio-Installer-Datei in das Stammverzeichnis des Repositorys.
- Passe die Datei `build-cmd.sh` im Stammverzeichnis des Repositorys an und setze die korrekte Versionsnummer entsprechend der heruntergeladenen Version. Ganz oben findest du die FMOD Studio-Versionsnummer, die du packen möchtest (eine kurze Version ohne Trennzeichen und eine lange Version):

```
FMOD_VERSION="20102"
FMOD_VERSION_PRETTY="2.01.02"
```

Fahre in der Windows-Eingabeaufforderung fort:

```
c:
cd \firestorm\3p-fmodstudio
autobuild build -A 64 --all
autobuild package -A 64 --results-file result.txt
```

Während der Ausführung des Autobuild-Build-Befehls könnte Windows nachfragen, ob Änderungen am Computer vorgenommen werden dürfen. Dies liegt am FMOD Studio-Installer. Erlaube diese Änderungen.

Am Ende der Ausgabe siehst du den Paketnamen:

```
wrote  C:\firestorm\3p-fmodstudio\fmodstudio-{version#}-windows64-{build_id}.tar.bz2''
```

Hier ist `{version#}` die FMOD Studio-Version (z.B. 2.01.02) und `{build_id}` eine interne Build-ID des Pakets. Zusätzlich wird eine Datei `result.txt` erstellt, die den MD5-Hash-Wert der Paketdatei enthält, den du im nächsten Schritt benötigst.

```
cd \firestorm\phoenix-firestorm
cp autobuild.xml my_autobuild.xml
set AUTOBUILD_CONFIG_FILE=my_autobuild.xml
```

Kopiere den FMOD Studio-Pfad und MD5-Wert aus dem Paketierungsprozess in diesen Befehl:

`autobuild installables edit fmodstudio platform=windows64 hash=<MD5-Wert> url=file:///<FMOD-Studio-Pfad>`

Beispiel:

`autobuild installables edit fmodstudio platform=windows64 hash=a0d1821154e7ce5c418e3cdc2f26f3fc url=file:///C:/firestorm/3p-fmodstudio/fmodstudio-2.01.02-windows-192171947.tar.bz2`

> [!NOTE]
> Das Kopieren von `autobuild.xml` und das Anpassen der Kopie innerhalb eines geklonten Repositorys ist zwar aufwändig für jedes Repository, das du erstellst, aber dies ist die einzige Möglichkeit, sicherzustellen, dass du Änderungen an `autobuild.xml` aus dem Upstream-Repository übernimmst und keine modifizierte `autobuild.xml` bei einem `git push` hochlädst.

## Viewer konfigurieren

Öffne die Windows-Eingabeaufforderung.

Falls du mit FMOD Studio baust und die vorherigen FMOD Studio-Einrichtungsanweisungen befolgt hast **UND** du jetzt ein neues Terminal verwendest, musst du zuerst die Umgebungsvariable zurücksetzen, indem du eingibst:

`set AUTOBUILD_CONFIG_FILE=my_autobuild.xml`

Dann gib ein:

```
c:
cd \firestorm\phoenix-firestorm
autobuild configure -A 64 -c ReleaseFS_open
```

Dies konfiguriert Firestorm mit allen Standardeinstellungen und ohne Drittanbieter-Bibliotheken.

Verfügbare vordefinierte Firestorm-spezifische Build-Targets:

```
ReleaseFS             (enthält KDU, FMOD)
ReleaseFS_open        (ohne KDU, ohne FMOD)
RelWithDebInfoFS_open (ohne KDU, ohne FMOD)
```

> [!TIP]
> Die erste Konfiguration des Viewers dauert etwas, da alle benötigten Drittanbieter-Bibliotheken heruntergeladen werden müssen. Der Download-Fortschritt ist standardmäßig ausgeblendet. Falls du den Fortschritt sehen möchtest, kannst du die Option `-v` für eine detailliertere Ausgabe verwenden:  
> `autobuild configure -A 64 -v -c ReleaseFS_open`

### Konfigurationsschalter

Es gibt mehrere Schalter, mit denen du den Konfigurationsprozess anpassen kannst. Der Name jedes Schalters wird gefolgt von seinem Typ und dem Wert, den du setzen möchtest.

- `-A <Architektur>` setzt die Zielarchitektur, also ob du einen 32-Bit- oder 64-Bit-Viewer bauen möchtest (falls weggelassen, ist 32-Bit der Standard).
- `--fmodstudio` steuert, ob das FMOD Studio-Paket in den Viewer eingebunden wird. Du musst die FMOD Studio-Installationsschritte unter [FMOD Studio mit Autobuild](#fmod-studio-mit-autobuild) durchgeführt haben, damit dies funktioniert.
- `--package` stellt sicher, dass alle Dateien in das Ausgabeverzeichnis des Viewers kopiert werden. Du wirst deinen kompilierten Viewer nicht starten können, wenn du `package` nicht aktivierst oder ihn nicht in VS "kompilierst".
- `--chan <Kanalname>` ermöglicht dir, einen benutzerdefinierten Kanalnamen für den Viewer festzulegen.
- `-LL_TESTS:BOOL=<bool>` steuert, ob Tests kompiliert und ausgeführt werden. Es gibt viele davon, daher wird empfohlen, sie auszuschließen, es sei denn, du hast einen bestimmten Grund, einen oder mehrere davon zu benötigen.

> [!TIP]
> **OFF** und **NO** sind dasselbe wie **FALSE**; alles andere wird als **TRUE** betrachtet.

Beispiele:

- Um einen 64-Bit-Viewer mit FMOD Studio und einem Installer-Paket zu bauen, führe diesen Befehl in der Windows-Eingabeaufforderung aus:  
  `autobuild configure -A 64 -c ReleaseFS_open -- --fmodstudio --package --chan MyViewer -DLL_TESTS:BOOL=FALSE`

- Um einen 64-Bit-Viewer ohne FMOD Studio und ohne Installer-Paket zu bauen, führe diesen Befehl aus:  
  `autobuild configure -A 64 -c ReleaseFS_open -- --chan MyViewer -DLL_TESTS:BOOL=FALSE`

## Viewer bauen

Es gibt zwei Möglichkeiten, den Viewer zu bauen: Über die Windows-Eingabeaufforderung oder innerhalb von Visual Studio.

### Bauen über die Windows-Eingabeaufforderung

Falls du mit FMOD Studio baust und die vorherigen FMOD Studio-Einrichtungsanweisungen befolgt hast **UND** du jetzt ein neues Terminal verwendest, musst du zuerst die Umgebungsvariable zurücksetzen mit:

`set AUTOBUILD_CONFIG_FILE=my_autobuild.xml`

Dann führe den Autobuild-Build-Befehl aus. Stelle sicher, dass du denselben Architekturparameter verwendest, den du bei der [Viewer-Konfiguration](#viewer-konfigurieren) gewählt hast:

`autobuild build -A 64 -c ReleaseFS_open --no-configure`

Die Kompilierung wird einige Zeit in Anspruch nehmen.

### Bauen innerhalb von Visual Studio

Im Firestorm-Quellordner findest du einen Ordner namens `build-vc170-<Architektur>`, wobei `<Architektur>` entweder 32 oder 64 ist, je nachdem, was du während der Konfiguration gewählt hast. In diesem Ordner befindet sich die Visual Studio-Projektmappendatei für Firestorm, genannt `Firestorm.sln`.

- Doppelklicke auf `Firestorm.sln`, um die Firestorm-Projektmappe in Visual Studio zu öffnen.
- Wähle im Menü Build -> Build Solution
- Warte, bis der Build abgeschlossen ist

## Fehlerbehebung

### SystemRootsystem32: unbound variable

Beim Ausführen des Autobuild-Build-Befehls kann ein Fehler wie folgt auftreten:

`../build.cmd.sh line 200: SystemRootsystem32: unbound variable`

Dieser Fehler wird durch die Reihenfolge der Einträge in der Windows-"path"-Umgebungsvariable verursacht. Autobuild exportiert alle Pfade, die in der "path"-Umgebungsvariable gesetzt sind, in Cygpath-Namen und -Variablen. Da diese Windows-"Pfade" auch Variablen wie `%SystemRoot%` enthalten können und voneinander abhängen können, ist es wichtig, die Abhängigkeitsreihenfolge beizubehalten. Beispiel:

```
%SystemRoot%
%SystemRoot%\system32
%SystemRoot%\System32\Wbem
```

Stelle sicher, dass die genannten Einträge die ersten in der "path"-Umgebungsvariable sind.

--- 
