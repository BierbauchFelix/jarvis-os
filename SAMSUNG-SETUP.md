# Jarvis auf dem Samsung S9 — Schritt für Schritt

Das S9 wird damit zur Alexa-Kopie: **„Jarvis" sagen → lila Ring → Befehl →
Aktion + gesprochene Antwort.** Display bleibt dauerhaft an, nachts wird
automatisch gedimmt (22:00–6:30), nach 1 Minute springt jede Ansicht zurück
aufs zuletzt genutzte Dashboard (Musik & Timer bleiben offen).

---

## Teil 1 — Vorbereitung in Home Assistant (am PC, ~10 Min)

### 1.1 Die Jarvis-Datei auf den Server legen
1. HA öffnen → **Einstellungen → Add-ons → Add-on-Store** → **File editor**
   installieren & starten.
2. Im File editor: im Ordner `/config` einen Ordner **`www`** anlegen
   (falls er noch nicht existiert).
3. Neue Datei **`/config/www/jarvis.html`** anlegen und den **kompletten
   Inhalt der aktuellen `index.html`** aus diesem Repo hineinkopieren
   (github.com/BierbauchFelix/jarvis-os → `index.html` → Raw → alles
   markieren → kopieren → einfügen → speichern).
4. War der `www`-Ordner neu: **HA einmal neu starten**
   (Einstellungen → System → Neu starten).

### 1.2 Zugriffstoken erstellen
1. In HA unten links auf deinen **Benutzernamen** (Profil) → Reiter
   **Sicherheit** → ganz unten **„Langlebige Zugriffstoken"** →
   **Token erstellen** → Name „Jarvis S9".
2. Den langen Token-Text **sofort kopieren** und irgendwo zwischenspeichern —
   er wird nur ein einziges Mal angezeigt!

### 1.3 Geräte-Skripte anlegen (damit „Netflix an" & Co. real schalten)
Unter **Einstellungen → Automatisierungen & Szenen → Skripte** diese Skripte
anlegen (die Namen müssen exakt stimmen — dieselben nutzt auch das Tab5):

| Skript-Entität | Soll auslösen |
|---|---|
| `script.tv_netflix` | Netflix am Android TV starten |
| `script.tv_youtube` | YouTube starten |
| `script.tv_disney`  | Disney+ starten |
| `script.tv_prime`   | Prime Video starten |
| `script.tv_hdmi`    | HDMI-Eingang wechseln |
| `script.pc_anschalten` | PC per Wake-on-LAN wecken |

### 1.4 Entitäten für Assist freigeben (Gemini darf nur Freigegebenes sehen)
**Einstellungen → Sprachassistenten → Reiter „Freigeben"** → freigeben:
Lichter, `weather.forecast_home`, Media-Player, ggf. Skripte.

---

## Teil 2 — Einrichtung auf dem Samsung S9 (~10 Min)

### 2.1 Mikrofon für den lokalen Server freischalten (einmalig)
Chrome erlaubt Mikrofonzugriff normalerweise nur auf https-Seiten.
Da Jarvis vom lokalen Server kommt (http), einmalig freischalten:
1. In Chrome öffnen: `chrome://flags/#unsafely-treat-insecure-origin-as-secure`
2. In das Textfeld eintragen: `http://192.168.0.101:8123`
3. Auf **Enabled** stellen → Chrome neu starten (Knopf unten).

### 2.2 Jarvis öffnen & verbinden
1. Sicherstellen, dass das S9 im **richtigen WLAN** ist (ZimmerFelix bzw.
   ein Netz, das `192.168.0.101` erreicht — kurz testen:
   `http://192.168.0.101:8123` muss im Browser aufgehen).
2. Öffnen: **`http://192.168.0.101:8123/local/jarvis.html`**
3. Mikrofon-Abfrage mit **Zulassen** beantworten.
4. **Zahnrad ⚙ oben rechts** → Bereich **Home Assistant**:
   - Adresse: `http://192.168.0.101:8123`
   - Token: den Token aus Schritt 1.2 einfügen → **💾**
   - Status muss auf **„✅ Verbunden"** springen.
5. Optional im selben Menü: **Spotify** verbinden (Client-ID) und
   **Govee**-API-Key eintragen — beides funktioniert direkt vom Handy.

### 2.3 Als App einrichten (ohne Browser-Leiste)
1. Chrome-Menü (⋮) → **„Zum Startbildschirm hinzufügen"**.
2. Ab jetzt immer über das **Jarvis-Icon** starten → randlose Vollansicht.

### 2.4 Display dauerhaft an
- Die Seite hält das Display selbst wach (WakeLock), solange sie offen ist.
- Zusätzlich absichern: **Einstellungen → Entwickleroptionen → „Aktiv
  lassen"** aktivieren (Display schläft nie am Ladegerät).
  (Entwickleroptionen freischalten: Einstellungen → Telefoninfo →
  Softwareinformationen → 7× auf „Buildnummer" tippen.)
- Nachts dimmt Jarvis von selbst (22:00–6:30, Befehl: „Nachtmodus aus/an").

### 2.5 Kiosk-Modus (optional, gegen versehentliches Rauswischen)
- Einfach: **Einstellungen → Sicherheit → „App-Anheftung"** aktivieren →
  App-Übersicht → Jarvis-Icon antippen → **Anheften**.
  (Nach einem Neustart einmal neu anheften.)
- Robust: App **„Fully Single App Kiosk"** installieren und auf Chrome
  festlegen — startet nach jedem Neustart automatisch. Wichtig: NICHT den
  normalen „Fully Kiosk Browser" für die Seite nutzen — dessen
  Browser-Engine (WebView) kann keine Spracherkennung, Jarvis wäre taub!

---

## Teil 3 — Testen

| Sag … | Es passiert |
|---|---|
| „Jarvis" | Lila Ring — er hört zu |
| „… wie spät ist es?" | Uhrzeit, gesprochen |
| „… mach das Licht an" | Govee direkt (mit Key) |
| „… Netflix an" | Start-Animation + echtes HA-Skript |
| „… was ist 12 mal 7?" | Rechenzentrum mit Ergebnis |
| „… timer 5 Minuten" | Timer-Ring, Ansage bei Ablauf |
| „… notiz Milch kaufen" | Notiz gespeichert |
| „… mach dich lauter / leiser" | Stimme ±20 % |
| „… wetter" | Wetterstation (nach 1 Min zurück) |
| beliebige Frage | Antwort über HA/Gemini, gesprochen |

## Wichtig zu wissen

- Über **github.io** (die öffentliche Version) funktioniert alles außer der
  HA-Verbindung — Browser blockieren https→http. Die Vollversion läuft
  deshalb **vom Home Server** (`/local/jarvis.html`).
- Nach Änderungen an `index.html` im Repo: Datei erneut nach
  `/config/www/jarvis.html` kopieren (Schritt 1.1.3).
