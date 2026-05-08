# Bauplan: Eigener City of Heroes Server (lokal, Single-PC)

> Basierend auf der YouTube-Anleitung "City of Heroes – Creating your own server guide"
> und dem offiziellen OuroDev-Wiki (Project Ouroboros / i25)

---

## Überblick

**City of Heroes** (CoH) ist ein legendäres Superhelden-MMORPG, das 2012 von NCSoft abgeschaltet
wurde. Dank der Community (Project Ouroboros / Homecoming) läuft es heute wieder auf privaten Servern –
offiziell von NCSoft lizenziert.

Ziel: Einen **i25-Server** auf einem Windows-PC betreiben, darauf als Admin und gleichzeitig
als Spieler mit GM-Rechten spielen.

**Stack:**
| Komponente | Software | Zweck |
|---|---|---|
| Server-Software | **COH-Server i25** (OuroDev) | Stellt den CoH-Spielserver bereit |
| Datenbank | **SQL Server Express** | Speichert Charaktere, Accounts, Welt |
| DB-Verwaltung | **SQL Server Management Studio (SSMS)** | Datenbank einrichten und verwalten |
| Client-Launcher | **Tequila** oder **Sunrise Launcher** | Patcht und startet den CoH-Client |
| Client-Dateien | **i25 CoH Client** (OuroDev) | Das eigentliche Spiel |
| Account-Portal | **CoH Account Portal** (PHP/XAMPP) | Accounts anlegen, Passwörter ändern |
| Webserver | **XAMPP** (im COH-xampp.7z enthalten) | Betreibt das Account-Portal lokal |

---

## Systemvoraussetzungen

| Ressource | Minimum | Empfohlen |
|---|---|---|
| RAM | 8 GB | **32 GB** (kritisch!) |
| Festplatte | 20 GB frei | 40 GB SSD |
| OS | Windows 10 64-bit | Windows 10/11 64-bit |
| CPU | Quad-Core | Quad-Core oder besser |

> **Wichtig:** Wer weniger als 32 GB RAM hat, sollte die Windows-Auslagerungsdatei
> auf 32.768 MB erhöhen (Systemsteuerung → Erweiterte Systemeinstellungen → Leistung → Virtueller Speicher).

---

## Phase 1 – Downloads

### 1.1 Ordnerstruktur anlegen
```
C:\CoH\
  ├── Server\       ← Entpackter COH-Server.7z
  ├── Client\       ← i25 CoH-Clientdateien
  ├── XAMPP\        ← Entpackter COH-xampp.7z
  └── dbschema\     ← Entpacktes dbschema.rar
```

### 1.2 Benötigte Downloads

| # | Software | Quelle |
|---|---|---|
| 1 | **COH-Server.7z** | wiki.ourodev.com/I25_Downloads |
| 2 | **COH-xampp.7z** | wiki.ourodev.com/I25_Downloads |
| 3 | **dbschema.rar** | wiki.ourodev.com/I25_Downloads |
| 4 | **i25 CoH Client** | wiki.ourodev.com/I25_CoH_Client_and_Server |
| 5 | **SQL Server Express** | Microsoft (kostenlos) |
| 6 | **SQL Server Management Studio** | Microsoft (kostenlos) |
| 7 | **7-Zip** | 7-zip.org (zum Entpacken der .7z-Dateien) |

---

## Phase 2 – Datenbank einrichten

### 2.1 SQL Server Express installieren
- Installation: Standardpfad `C:\Microsoft SQL Server` (wichtig – sonst müssen DBSchema-Dateien angepasst werden)
- Instanzname: `SQLEXPRESS` (Standard beibehalten)

### 2.2 Datenbank-Schema importieren
1. `dbschema.rar` nach `C:\CoH\dbschema\` entpacken
2. SSMS öffnen → Verbindung zu `localhost\SQLEXPRESS`
3. SQL-Skripte aus `dbschema\` der Reihe nach ausführen
4. Ergebnis: CoH-Datenbank mit allen benötigten Tabellen ist angelegt

---

## Phase 3 – Server konfigurieren

### 3.1 COH-Server.7z entpacken
```
Entpacken nach: C:\CoH\Server\
```

### 3.2 Konfigurationsdateien anpassen

Folgende `.cfg`-Dateien in `C:\CoH\Server\bin\` öffnen und das Passwort setzen:

| Datei | Anpassung |
|---|---|
| `servers.cfg` | Passwort setzen, `DefaultAccessLevel` einstellen |
| `account_server.cfg` | DB-Verbindung + Passwort |
| `auction_server.cfg` | DB-Verbindung + Passwort |
| `chat_server.cfg` | DB-Verbindung + Passwort |

**Beispiel `servers.cfg` – GM-Rechte für alle neuen Chars:**
```ini
DefaultAccessLevel = 11
```
→ Alle neu erstellten Charaktere erhalten automatisch maximale GM-Rechte.
→ Nach Änderung: DBServer neu starten oder reload ausführen.

### 3.3 Server starten
Die Server-Prozesse müssen **in dieser Reihenfolge** gestartet werden:
```
1. DBServer.exe
2. AuthServer.exe
3. MapServer.exe
4. (weitere Zonen-Server nach Bedarf)
```
Alle Prozesse laufen als Konsolenfenster. Wenn alle „Listening" melden → Server bereit.

---

## Phase 4 – Client einrichten

### 4.1 XAMPP + Tequila (aus COH-xampp.7z)
1. `COH-xampp.7z` nach `C:\CoH\XAMPP\` entpacken
2. `Tequila.exe` aus dem Paket verwenden
3. Tequila zeigt ein Manifest-Feld → Manifest-URL eintragen:
   ```
   http://127.0.0.1/manifest.xml
   ```
4. Client-Pfad auf `C:\CoH\Client\` zeigen lassen
5. „Update" und dann „Play" klicken

### 4.2 Direkt mit dem Server verbinden
- Der Client verbindet automatisch mit `127.0.0.1` (lokal)
- Standard-Port: `7099` (AuthServer)

---

## Phase 5 – Account anlegen & einloggen

### 5.1 Account-Portal (CoH Account Portal)
Das mitgelieferte XAMPP enthält ein Web-Portal:
1. XAMPP-Webserver starten
2. Browser öffnen: `http://127.0.0.1/`
3. Account erstellen: Benutzername + Passwort → in Datenbank speichern
4. Mit diesem Account in Tequila einloggen

### 5.2 Erster Login
- In Tequila: Account-Daten eingeben → Spielen
- Charakter erstellen → mit `DefaultAccessLevel = 11` sofort GM-Rechte

---

## Phase 6 – Als GM spielen

### 6.1 Admin-Menü öffnen
```
Im Spiel tippen: /mmm
```
→ Öffnet das Admin-Menü (benötigt AccessLevel 10)
→ Viele Menüpunkte erfordern AccessLevel 11

### 6.2 Wichtige GM-Befehle

| Befehl | Funktion |
|---|---|
| `/mmm` | Admin-Hauptmenü öffnen |
| `/gm` | GM-Modus umschalten |
| `/setaccesslevel <name> <level>` | Spieler AccessLevel setzen |
| `/goto <zone>` | Zu einer Zone teleportieren |
| `/spawn <entity>` | Gegner/NPC spawnen |
| `/killall` | Alle Gegner in der Nähe töten |
| `/godmode` | Unverwundbarkeit |
| `/fly` | Flugmodus |
| `/speed` | Bewegungsgeschwindigkeit erhöhen |
| `/noclip` | Durch Wände gehen |
| `/showpophelp 0` | Hilfsfenster ausschalten |

### 6.3 AccessLevel-Übersicht

| Level | Rolle |
|---|---|
| 0 | Normaler Spieler |
| 1–4 | GM-Stufen |
| 9 | Admin |
| 10 | System-Admin (`/mmm` möglich) |
| **11** | **Voller Zugriff (empfohlen für Eigentümer)** |

---

## Phase 7 – Als normaler Spieler spielen

- Zweiten Account im Portal anlegen → `DefaultAccessLevel = 0` (normaler Spieler)
- Mit dem Tequila-Launcher zwischen Accounts wechseln
- Beide Accounts spielbar auf demselben lokalen Server

---

## Schritt-für-Schritt Zusammenfassung

```
[1]  7-Zip installieren
[2]  SQL Server Express + SSMS installieren (Pfad: C:\Microsoft SQL Server)
[3]  Downloads: COH-Server.7z, COH-xampp.7z, dbschema.rar, i25 Client
[4]  Datenbank-Schema importieren via SSMS
[5]  COH-Server.7z nach C:\CoH\Server\ entpacken
[6]  .cfg-Dateien anpassen (Passwörter, DefaultAccessLevel = 11)
[7]  Server starten: DBServer → AuthServer → MapServer
[8]  XAMPP starten (aus COH-xampp.7z)
[9]  Account anlegen im Web-Portal (http://127.0.0.1/)
[10] Tequila starten → Manifest: http://127.0.0.1/manifest.xml
[11] Client updaten und starten
[12] Einloggen → Charakter erstellen → /mmm für GM-Menü
```

---

## Nächste Schritte (Phase 2 des Projekts)

- [ ] SQL Server Express heruntergeladen und installiert
- [ ] Alle OuroDev-Downloads abgeschlossen
- [ ] Datenbank-Schema erfolgreich importiert
- [ ] Server startet ohne Fehler
- [ ] Client verbindet sich mit lokalem Server
- [ ] GM-Zugriff via `/mmm` bestätigt
- [ ] Optional: Modding (eigene Maps, Missionen, Klassen)

---

**Quellen:**
- [YouTube: City of Heroes – Creating your own server guide](https://www.youtube.com/watch?v=XhLgDrk6WiY)
- [OuroDev Wiki – I25 Server Setup](https://wiki.ourodev.com/index.php?title=I25_Server_Setup)
- [OuroDev Wiki – I25 Downloads](https://wiki.ourodev.com/view/I25_Downloads)
- [OuroDev Wiki – I25 Commands](https://wiki.ourodev.com/I25_Commands)
- [GitHub – CoH Account Portal](https://github.com/auroris/City-of-Heroes-Account-Portal)
- [Homecoming – Größter öffentlicher CoH-Server](https://forums.homecomingservers.com/getting-started/)
