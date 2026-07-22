# Jarvis Pi-Display — Raspberry Pi 2 als Sprach-gesteuerte Anzeige

`jarvis-pi.html` ist die Leichtbau-Version des Jarvis-Designs: keine Filter,
keine Dauer-Animationen, Neuzeichnen nur bei Datenänderung — läuft dadurch
auch auf einem Raspberry Pi 2 flüssig. Die Seite hört selbst NICHT zu; sie
reagiert auf Sprach-Signale vom Home Server (Blue Yeti, Tab5, S9) und
wechselt die Ansicht.

---

## Teil 1 — Home Assistant vorbereiten

### 1.1 Seiten-Helfer anlegen
**Einstellungen → Geräte & Dienste → Helfer → Helfer erstellen → Text**
→ Name: **Jarvis Seite** (ergibt `input_text.jarvis_seite`).

### 1.2 Datei auf den Server
`jarvis-pi.html` per File editor nach **`/config/www/jarvis-pi.html`**
kopieren (Ordner `www` ggf. anlegen → HA 1× neu starten).

### 1.3 Sprach-Automationen (Satz → Seitenwechsel)
File editor → `/config/automations.yaml` → unten anhängen
(danach: Entwicklerwerkzeuge → YAML → Automationen neu laden):

```yaml
- alias: "Jarvis-Anzeige: Wetter zeigen"
  triggers:
    - trigger: conversation
      command:
        - "zeig[e] [das] wetter"
        - "wetter anzeigen"
  actions:
    - action: input_text.set_value
      target: { entity_id: input_text.jarvis_seite }
      data: { value: "wetter" }

- alias: "Jarvis-Anzeige: Musik zeigen"
  triggers:
    - trigger: conversation
      command: ["zeig[e] [die] musik", "musik anzeigen"]
  actions:
    - action: input_text.set_value
      target: { entity_id: input_text.jarvis_seite }
      data: { value: "musik" }

- alias: "Jarvis-Anzeige: Lichter zeigen"
  triggers:
    - trigger: conversation
      command: ["zeig[e] [die] lichter", "lichter anzeigen"]
  actions:
    - action: input_text.set_value
      target: { entity_id: input_text.jarvis_seite }
      data: { value: "lichter" }

- alias: "Jarvis-Anzeige: Notizen zeigen"
  triggers:
    - trigger: conversation
      command: ["zeig[e] [die] notizen", "notizen anzeigen"]
  actions:
    - action: input_text.set_value
      target: { entity_id: input_text.jarvis_seite }
      data: { value: "notizen" }

- alias: "Jarvis-Anzeige: zurück zum Dashboard"
  triggers:
    - trigger: conversation
      command: ["zeig[e] [das] dashboard", "startseite", "anzeige zurück"]
  actions:
    - action: input_text.set_value
      target: { entity_id: input_text.jarvis_seite }
      data: { value: "home" }
```

**Wichtig zu wissen:** Die Kommandos sind bewusst „zeig …"-Formulierungen.
Fragen wie „wie ist das Wetter?" gehen weiterhin an Gemini und werden
GESPROCHEN beantwortet — würde man sie als Auslöser abfangen, gäbe es keine
Sprach-Antwort mehr. „Zeig das Wetter" = Anzeige wechselt; „Wie ist das
Wetter?" = Jarvis antwortet per Stimme. Beides zusammen geht auch:
„Zeig das Wetter" sagen, dann die Frage stellen.

Die Anzeige springt nach **60 Sekunden** automatisch zurück aufs Dashboard
(außer Musik spielt gerade und die Musik-Seite ist offen).

---

## Teil 2 — Raspberry Pi 2 einrichten

1. **Raspberry Pi OS Lite + Chromium-Kiosk** oder einfacher: das fertige
   Kiosk-Image **FullPageOS** auf die SD-Karte flashen und als URL eintragen:
   `http://192.168.0.101:8123/local/jarvis-pi.html`
2. Bei normalem Raspberry Pi OS, Autostart in
   `~/.config/lxsession/LXDE-pi/autostart`:
   ```
   @chromium-browser --kiosk --noerrdialogs --disable-infobars --incognito http://192.168.0.101:8123/local/jarvis-pi.html
   ```
   (`--incognito` weglassen, sobald Token gespeichert werden soll!)
3. Beim ersten Start erscheint die **Einrichtung** direkt auf dem Display:
   HA-Adresse, Token (HA → Profil → Sicherheit → langlebiges Token) und
   Entitäten eintragen → Speichern. Alles bleibt lokal im Browser.
4. Einrichtung später wieder öffnen: **3× schnell auf die Statuszeile
   unten tippen/klicken.**
5. Bildschirmschoner aus: `sudo raspi-config` → Display → Screen Blanking → Off.

## Seiten & Signale

| Wert im Helfer | Anzeige |
|---|---|
| `home` | Uhr, Datum, Wetterzeile, Now-Playing-Zeile |
| `wetter` | Wetterstation + 5-Tage-Vorschau |
| `musik` | Titel, Künstler, Fortschrittsbalken |
| `lichter` | Beide Lichter mit An/Aus + Helligkeit |
| `notizen` | Inhalt von `input_text.jarvis_notizen` |
