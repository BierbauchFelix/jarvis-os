# Jarvis OS — Holographic HUD Dashboard

Ein voll funktionsfähiges Sprachassistenten-Dashboard im Stil eines holografischen Jarvis-Interfaces. Eine einzige, in sich geschlossene `index.html` (SVG + Vanilla JS + CSS) — keine Build-Tools, kein Backend nötig.

## Design

- **Holografischer Jarvis-Core** statt Roboter-Avatar: rotierende Ringe, Reaktor-Geometrie, glühender Kern. Reagiert auf Zuhören & Zustand.
- **Colorway:** Navy/Indigo mit **Dark-Lila** als Akzent (`#5566cc → #7d63d8 → #9b6ce0`).
- **Idle 1 & Idle 2:** beide mit dezenter K2-Bergmassiv-Illustration im Hintergrund.
- **Futuristische HUD-Panels** mit Eck-Klammern, Neon-Kanten und Status-Zeilen.

## Funktionen

- 🎙️ Sprachsteuerung (Wake-Word „Hey Jarvis") & Text-Eingabe
- ⏱️ Mehrfach-Timer mit Countdown auf dem Startbildschirm & Nebeneinander-Ansicht
- 🧮 Rechner mit animierter HUD-Anzeige
- 📝 Notizen & Erinnerungen (mit Datum/Uhrzeit, Benachrichtigung)
- 🌤️ Wetter, Luftqualität, Pollen, UV, Sonnenauf-/untergang
- 💡 Govee-Lichtsteuerung (futuristischer HUD-Panel)
- 🎵 Spotify Now-Playing & Steuerung
- 🚗 Fahrtzeit-Rechner (Punkt A → B, OSRM)
- 🚆 ÖPNV-Verbindungen
- 🖥️ Computer-/Fernseher-Start-Animationen mit App-Covern
- 📖 Wissens-Antworten („Was ist …")

## Nutzung

Einfach `index.html` im Browser öffnen. API-Schlüssel (Groq für KI-Antworten, Govee für Lichter, Spotify) werden im Command-Center rechts eingetragen und lokal im Browser (`localStorage`) gespeichert — sie landen **nicht** im Code.

## Hosting

Läuft als statische Seite überall (GitHub Pages, Netlify, lokaler Datei-Aufruf).
