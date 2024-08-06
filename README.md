**Hinweis: Vor der Umstellung ein Backup aller Server erstellen!**

# Symlink Manager for ASA Manager

Dieses Skript ermöglicht die Migration von ARK-Servern des ASA Managers zu einer Struktur mit Junctions/Symlinks.

## Wofür brauche ich das?

- **Reduzierung des Speicherbedarfs**: Ein ARK-Server belegt ca. 12 GB Speicherplatz. Ohne Junctions benötigt jeder Server diese 12 GB. Bei 6 Servern wären das 72 GB. Mit Junctions belegen alle 6 Server zusammen nur noch 12 GB (zuzüglich Savegames, Backups usw.).
- **Schnellere Updates**: Es muss nur ein Update durchgeführt werden, und alle Server sind auf dem neuesten Stand.
- **Schnelleres Starten der Server**: Mods werden während des Startvorgangs heruntergeladen. Wenn ein Server gestartet wurde, müssen die anderen Server die Mod-Updates nicht erneut herunterladen.

## Anforderungen

- ASA (Dedicated Server) Manager (Version ...)
- Windows PowerShell (Version 5.0 oder höher)
- NTFS-Dateisystem
- Berechtigungen zum Erstellen von Junctions/Symlinks (Administratorrechte oder entsprechende Berechtigungen)
- **WICHTIG**: Die vom ASA Manager verwalteten Server müssen alle in einem separaten Verzeichnis liegen (es dürfen keine anderen Verzeichnisse vorhanden sein) - siehe dazu den Nachtrag "Serverstruktur" unten


## Welche Scripte sind enthalten

- `migration.ps1` - Migriert von der normalen Verzeichnisstruktur zur Junctions-Struktur
- `check_migration.ps1` - Überprüft, ob die Voraussetzungen erfüllt sind und ob der aktuelle Benutzer Junctions erstellen kann
- `delete_junctions.ps1` (in Planung) - Stellt den Ursprungszustand wieder her (prüft die Datenträgerkapazität)


## Anwendung

1. Kopiere die Dateien `migration.ps1`, `check_migration.ps1` und `delete_junctions.ps1` in das Verzeichnis, in dem die ARK-Server installiert sind.
2. Öffne eine PowerShell-Konsole (Windows Startbutton -> PowerShell eingeben -> Windows PowerShell starten).
3. Wechsel in das Verzeichnis der Server/Maps (siehe "Serverstruktur" unten).
4. Prüfe, ob die Migration möglich ist: `./check_migration.ps1`. Wenn der Test erfolgreich ist:
5. Führe das Migrationsskript aus: `./migration.ps1`.
6. Wähle die gewünschte Option:
   - (a) Migration aller Server:
     - Wähle einen Startserver aus. Es empfiehlt sich, den Server mit den meisten/größten Mods zu nehmen.
     - Ein neues Verzeichnis "CoreServer" wird erstellt und der ausgewählte Server wird dorthin kopiert (ohne Savegames etc.).
     - Die Serverdateien der anderen Server/Maps werden gelöscht und durch Junctions ersetzt.
   - (b) Migration eines neuen Servers:
     - Voraussetzung: Ein CoreServer muss bereits existieren.
     - Wähle das Verzeichnis des neuen Servers aus.
     - Die Migration wird durchgeführt, indem die Serverdateien gelöscht und durch Junctions ersetzt werden. Bereits migrierte Server werden übersprungen.

**Hinweis:** Der CoreServer wird NICHT direkt vom ASA Manager verwaltet. Updates erfolgen darin, aber der ASA Manager "weiß" das nicht. Der CoreServer ermöglicht es, alle bestehenden Maps zu löschen und mit Junctions neu zu erstellen, ohne den CoreServer versehentlich zu löschen.

Für die Rückgängigmachung der Migration nutze das Skript `delete_junctions.ps1` (in Arbeit).


## Nachtrag

### Serverstruktur

Das Skript setzt eine bestimmte Serverstruktur voraus. Der Name des Hauptverzeichnisses ist egal, aber der Aufbau darunter ist wichtig. Stellen Sie sicher, dass die Struktur korrekt ist. Nutzen Sie gegebenenfalls den ASA Manager, um die Server zu verschieben oder umzubenennen. Vermeiden Sie Sonderzeichen oder Leerzeichen im Pfad.

Beispielstruktur:
c:\GameServer\Ark_ASA_Manager\ # Hauptverzeichnis (der ASA Manager selbst)
c:\GameServer\Maps\ # Hauptverzeichnis für die Server (hier kommen die Skripte rein)
c:\GameServer\Maps\TheIsland\ # Verzeichnis für einen Server/Map (jedem Server ein eigenes Verzeichnis)
Weitere Server/Maps:
c:\GameServer\Maps\TheCenter\
c:\GameServer\Maps\Aberration\
c:\GameServer\Maps\ScorchedEarth\
c:\GameServer\Maps\Svartfalheim\

In jedem Serververzeichnis (z.B. `TheIsland`) sollten die folgenden Unterverzeichnisse vorhanden sein:
- `Engine`
- `steamapps`
- `steamcmd`
- `ShooterGame\Binaries`
- `ShooterGame\Content`
- `ShooterGame\Plugins`

Wenn diese Ordner fehlen, wurde die Struktur nicht korrekt erstellt. Korrigieren Sie dies mit dem ASA Manager.

## Todos
- Code sauber dokumentieren
- `delete_junctions.ps1`-Skript erstellen
