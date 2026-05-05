# Bauplan: Eigener Ultima Online Server (lokal, Single-PC)

> Basierend auf der YouTube-Playlist "Creating my own Ultima Online Server" (ServUO 57.3)

---

## Überblick

Ziel: Einen privaten UO-Server auf **einem Windows-PC** betreiben, darauf als Admin und gleichzeitig als Spieler mit GM-Rechten spielen.

**Stack:**
| Komponente | Software | Zweck |
|---|---|---|
| Server-Emulator | **ServUO** | Stellt den UO-Spielserver bereit |
| Spielclient | **ClassicUO** | Moderner Open-Source UO-Client |
| UO-Spieledaten | UO-Clientdateien (7.0.x) | Grafiken, Maps, Sounds |
| Launcher/Assistant | **Razor** oder **UOSteam** | Verbindet Client mit lokalem Server |
| Build-Tool | **.NET SDK** (min. .NET 6) | Kompiliert ServUO aus dem Quellcode |
| Versionskontrolle | **Git für Windows** | Lädt ServUO von GitHub |

---

## Phase 1 – Vorbereitung & Downloads

### 1.1 Ordnerstruktur anlegen
```
C:\UO-Server\
  ├── ServUO\          ← Server-Emulator
  ├── Clients\         ← UO-Spieledateien
  └── Tools\           ← Razor, ClassicUO
```

### 1.2 Benötigte Software herunterladen

| # | Software | Quelle | Hinweis |
|---|---|---|---|
| 1 | **Git für Windows** | git-scm.com | Wird zum Klonen von ServUO benötigt |
| 2 | **.NET SDK 8** | dot.net | ServUO benötigt .NET 6 oder höher |
| 3 | **ServUO Quellcode** | github.com/ServUO/ServUO | `git clone` in `C:\UO-Server\ServUO\` |
| 4 | **UO-Clientdateien** | mirror.ashkantra.de oder uo.com/client-download | Version 7.0.x classic; alle `.mul`/`.uop` Dateien |
| 5 | **ClassicUO Launcher** | classicuo.eu | Open-Source UO-Client (Modern-Renderer) |
| 6 | **Razor CE** | razorce.com | Assistant zum Verbinden mit lokalem Server |

---

## Phase 2 – ServUO Server installieren & kompilieren

### 2.1 Repository klonen
```bash
cd C:\UO-Server\ServUO
git clone https://github.com/ServUO/ServUO.git .
```

### 2.2 Server kompilieren
```
Doppelklick auf: Compile-Release.bat
→ Erzeugt: ServUO\Server\bin\Release\net6.0\ServUO.exe
```
Alternativ: `dotnet build` in der Kommandozeile.

### 2.3 UO-Clientdateien einbinden

Datei öffnen: `ServUO\Config\DataPath.cfg`
```
CustomPath=C:\UO-Server\Clients\
```
→ ServUO liest von dort Maps, Grafiken und Tile-Daten.

### 2.4 Server erstmalig starten

```
Doppelklick auf: ServUO.exe
```
Beim ersten Start:
- Server fragt nach **Admin-Accountname** und **Passwort** → sicher wählen!
- Danach erscheint im Terminal: `Listening on port 2593`

---

## Phase 3 – Spielclient einrichten (ClassicUO)

### 3.1 ClassicUO konfigurieren

1. ClassicUO Launcher starten
2. Profil anlegen:
   - **Server IP:** `127.0.0.1`
   - **Port:** `2593`
   - **UO-Datei-Pfad:** `C:\UO-Server\Clients\`
3. Speichern → ClassicUO starten

### 3.2 Razor CE als Mittler (empfohlen)

Razor CE kann zwischen ClassicUO und ServUO geschaltet werden:
- Server-IP in Razor: `127.0.0.1`, Port `2593`
- Razor-IP als "Server" in ClassicUO: `127.0.0.1`, Port `2595`

> Razor erweitert den Client um Makros, Auto-Heil, Targeting – nützlich auch für GM-Aktionen.

---

## Phase 4 – Als Admin & GM einloggen

### 4.1 Erster Login als Admin
- Im Client mit den **Admin-Zugangsdaten** (Phase 2.4) anmelden
- Im Spiel-Chat eingeben: `[admin` → öffnet das Admin-Panel

### 4.2 Admin-Rechte bestätigen
- In der ServUO-Konsole erscheint beim Login die Accountliste
- AccessLevel des Admin-Accounts: **Administrator** (höchste Stufe)

### 4.3 Shard bevölkern (Welt befüllen)
- Im Admin-Panel: **[populate]** oder über Konsole
- Alternativ: `[add` im Spiel → Objekte/Monster/NPCs manuell platzieren

### 4.4 GM-Charakter anlegen

Option A – Admin-Account direkt spielen:
- Gleicher Account wie Admin, AccessLevel bleibt Administrator
- Hat im Spiel alle GM-Befehle

Option B – Separater Spieler-Account mit GM-Rechten:
- In der ServUO-Konsole: `[account add <name> <passwort>`
- AccessLevel anheben: `[setaccess <accountname> GameMaster`
- So kann man Admin-Account und GM-Charakter trennen

---

## Phase 5 – Wichtige GM-Befehle im Spiel

| Befehl | Funktion |
|---|---|
| `[add` | Objekte/Kreaturen/Items platzieren |
| `[go` | Teleportieren zu Koordinaten oder Orten |
| `[where` | Eigene Koordinaten anzeigen |
| `[props` | Eigenschaften eines Objekts bearbeiten |
| `[ban` / `[kick` | Spieler sperren / rauswerfen |
| `[save` | Spielwelt manuell speichern |
| `[shutdown` | Server herunterfahren |
| `[tele` | Auf eine Fläche teleportieren (Ziel anklicken) |
| `[sethome` | Spawn-Punkt setzen |

---

## Phase 6 – Als normaler Spieler spielen (parallel)

Da alles auf einem PC läuft, kann man **denselben Client** nutzen:
1. Mit Admin-Account anmelden → als GM spielen
2. Oder: Zweiten Charakter auf gleichem Admin-Account erstellen (normaler Slot)
3. Oder: Separaten Account ohne Sonderrechte → reines Spielerlebnis

> Auf einem lokalen Server können auch mehrere Clients gleichzeitig laufen (z.B. Admin-Char + Test-Spieler).

---

## Schritt-für-Schritt Zusammenfassung

```
[1] Git installieren
[2] .NET SDK 8 installieren
[3] ServUO von GitHub klonen
[4] UO-Clientdateien herunterladen und in C:\UO-Server\Clients\ ablegen
[5] ServUO kompilieren (Compile-Release.bat)
[6] DataPath.cfg auf Clientdateien zeigen lassen
[7] ServUO.exe starten → Admin-Account anlegen
[8] ClassicUO Launcher installieren
[9] Profil in ClassicUO: IP 127.0.0.1, Port 2593, Dateipfad
[10] Einloggen mit Admin-Account
[11] [populate] ausführen → Welt füllt sich
[12] Spielen als GM / Admin
```

---

## Nächste Schritte (Phase 2 des Projekts)

- [ ] Alle Software-Downloads abgeschlossen
- [ ] ServUO erfolgreich kompiliert
- [ ] Erster Login als Admin funktioniert
- [ ] ClassicUO verbindet sich mit lokalem Server
- [ ] GM-Rechte im Spiel bestätigt
- [ ] Customizations (Regeln, Rassen, Karten) nach Wunsch anpassen

---

**Quellen:**
- [ServUO GitHub](https://github.com/ServUO/ServUO)
- [ServUO Windows Beginner Setup](https://www.servuo.dev/threads/windows-10-beginner-setup.14537/)
- [ClassicUO Client](https://www.classicuo.eu/)
- [UO Client Mirror](https://mirror.ashkantra.de/)
- [GMing ServUO Guide](https://uoserpentisle.com/index.php/2019/08/19/gming-servuo-part-1/)
- [YouTube Playlist: Creating my own UO Server](https://www.youtube.com/playlist?list=PL-Yc04ACO5iDOmpNbPmY2g1qSKb_3mN14)
