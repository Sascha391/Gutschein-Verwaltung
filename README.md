# 🎟️ Gutschein-Verwaltung

Eine interne Web-App für **Foto Riede**, um Gutscheine (u. a. für Fahrschulen) zu
erstellen, zu verwalten und einzulösen. Codes generieren, drucken/als PDF herunterladen
und per Eingabe einlösen – alles in einer Seite, vom Handy oder PC aus.

**▶ Zur App:** https://sascha391.github.io/Gutschein-Verwaltung/

> Zugang per PIN. Daten (Gutscheine, Einlösungen) liegen geschützt in Firebase.

## Funktionen
- **PIN-Login** über Firebase, Sitzung läuft nach 10 Stunden automatisch ab
- **Zwei Kategorien:**
  - **Aktions-Gutscheine** – beliebig viele Aktionen, je Aktion Gruppen (z. B. Fahrschulen)
  - **Normal-Gutscheine** – einzelne Gutscheine mit Für/Von, Wert und Bemerkung
- **Codes erzeugen** – eindeutige Codes (keine Dopplungen), mehrere auf einmal
- **Einlösen** – Schnell-Suche im Hauptmenü (über alle Aktionen) oder gezielt je Aktion
- **Zwei Gutschein-Vorlagen** (mit / ohne Namensfeld), als Bild gerendert
- **Download & Druck:**
  - Alle als **einzel-PDF (ZIP)** – pro Code eine PDF, Ordner nach Fahrschule benannt
  - Alle in **einer PDF** (mehrseitig, ein Gutschein pro Seite)
  - Alle als **JPEGs (ZIP)**
  - Einzelne Codes auswählen · Drucken (15 × 10 cm)
- **Statistik & Übersicht** – offen/eingelöst und Werte je Aktion
- **Export** als **CSV** und **Excel** (.xlsx)
- **Ablaufdatum** wird automatisch berechnet (einstellbare Gültigkeit je Aktion)
- **Hell-/Dunkel-Modus**, als **PWA installierbar**

## Technik
Reine Frontend-App – **kein eigener Server**. Firebase dient nur als **Backend**
(Datenbank + Login), das **Hosting läuft über GitHub Pages**.

- `index.html` – die komplette App (HTML, CSS, JavaScript)
- `manifest.json` – PWA-Infos (Name, Icon, Farben)
- `icons/icon-192.png`, `icons/icon-512.png` – App-Icons

**Firebase-Projekt:** `passbild-fahrschulen` (Firestore + Auth).
Firestore-Sammlungen: `aktionen_meta`, `aktionen_<id>` (Codes),
`aktionen_<id>_groups` (Gruppen/Notizen), `normal_gutscheine`.

Eingebundene Bibliotheken (per CDN): Firebase, **JSZip** (ZIP), **jsPDF** (PDF),
**SheetJS/xlsx** (Excel).

## Lokal testen
Die App nutzt ES-Module und lädt Firebase – daher **nicht** die Datei doppelklicken,
sondern über einen kleinen lokalen Server öffnen:

```
npx serve
```

(oder `python -m http.server 8080`) und dann im Browser z. B. `http://localhost:3000`
bzw. `http://localhost:8080` aufrufen.

## Veröffentlichen
Gehostet auf **GitHub Pages**. Es genügt, die Änderung in den `main`-Branch zu pushen –
die Live-Seite baut sich automatisch neu (dauert i. d. R. unter einer Minute):

```
git add -A
git commit -m "Beschreibung der Änderung"
git push
```

Ein Service Worker ist nicht aktiv, daher reicht nach dem Deploy ein normales Neuladen
(`Strg`+`F5`).
