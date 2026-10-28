# Konzept: Gemeinsame App für zwei Personen (Ausgaben, Kalender, To-dos)

## 1. Ziel

Eine App für zwei Personen (Person A und Person B), in der sie
1. alle Ausgaben das ganze Jahr über erfassen und auswerten,
2. einen eigenständigen Kalender mit Terminen für beide oder für eine Person führen,
3. To-do-Listen für sich selbst oder gemeinsam pflegen,
4. Push-Benachrichtigungen auf dem iPhone und dem Windows-PC erhalten.

## 2. Grundprinzipien

- **Nur zwei Nutzer.** Keine Registrierung, keine öffentliche Version.
- **Eigenständig.** Kein Sync mit iCloud, Google, Outlook oder Windows-Kalender. Keine Bankanbindung.
- **Ein Login, zwei Geräte.** Die App läuft im Browser und ist auf dem iPhone und dem PC nutzbar.
- **Zuordnung überall gleich.** Jeder Eintrag gehört zu Person A, Person B oder Gemeinsam.
- **Keine Schuldenrechnung.** Die Zuordnung dient nur der Übersicht.

## 3. Plattformen

- **iPhone:** Web-App im Safari, zum Home-Bildschirm hinzufügen (PWA). Push über Web-Push, ab iOS 16.4 möglich, wenn die App zum Home-Bildschirm hinzugefügt wurde.
- **Windows-PC:** Browser (Edge oder Chrome) oder als installierbare PWA. Push über Browser-Benachrichtigungen.
- **Empfohlene Technik:** eine Web-App (PWA), die auf beiden Geräten identisch funktioniert. Ein Codebestand, kein separates iOS- und Windows-Projekt.

## 4. Module

### 4.1 Ausgaben
- Erfassung: komplett manuell. Betrag, Datum, Beschreibung, Händler, Kategorie und Zuordnung werden selbst eingegeben.
- Belegfoto: Foto aufnehmen oder aus der Galerie wählen und an die Ausgabe anhängen. Keine automatische Erkennung, dadurch keine Kosten.
- Zuordnung: Person A, Person B oder Gemeinsam.
- Kategorien: Lebensmittel, Restaurant & Café, Haushalt, Miete, Transport, Tanken, Gesundheit, Freizeit, Kleidung, Reisen, Abos, Sonstiges. Eigene Kategorien möglich. Die App merkt sich Korrekturen pro Händler.
- Auswertung: Monats- und Jahresübersicht, Diagramme nach Kategorie und Zahler, Filter, Export (CSV, PDF).

### 4.2 Kalender
- Eigene Kalenderansicht: Tag, Woche, Monat.
- Termine haben eine Zuordnung: **Person A**, **Person B** oder **Gemeinsam**.
  - Gemeinsame Termine erscheinen bei beiden.
  - Persönliche Termine erscheinen nur bei der jeweiligen Person, außer die andere Person schaltet die Ansicht ein.
- Felder: Titel, Datum, Uhrzeit (oder ganztägig), Dauer, Ort, Notiz, Erinnerung.
- Wiederholungen: täglich, wöchentlich, monatlich, jährlich.
- Farben: eine Farbe pro Person, eine dritte für Gemeinsam.
- Erinnerung vor dem Termin (z. B. 15 Minuten, 1 Tag vorher) über Push.

### 4.3 To-do-Listen
- Aufgaben haben eine Zuordnung: **Person A**, **Person B** oder **Gemeinsam**.
- Eigene Listen oder Sammeltabs: "Für mich", "Für uns", "Für ihn/sie" (jeweils für die Personen sichtbar).
- Felder: Titel, Notiz, Fälligkeitsdatum, optional Uhrzeit, Erledigt-Status, Erinnerung.
- Aufgaben können mit einem Termin verknüpft werden (z. B. "Geschenk besorgen" bei "Geburtstag").
- Erledigte Aufgaben werden ausgeblendet, aber nicht gelöscht.

### 4.4 Push-Benachrichtigungen
- **Auf dem iPhone:** Web-Push über die installierte PWA. Einmalige Zustimmung beim ersten Öffnen.
- **Auf dem Windows-PC:** Browser-Benachrichtigungen, solange der Browser geöffnet ist oder als PWA installiert ist.
- **Auslöser:**
  - Termin-Erinnerung (frei wählbar)
  - Fällige Aufgabe
  - Neue Ausgabe, neuer Termin oder neue Aufgabe der anderen Person (standardmäßig an, pro Typ abschaltbar)
- Jede Person entscheidet selbst, welche Benachrichtigungen sie bekommt.

## 5. Benutzer und Zugriff

- Zwei feste Konten: Person A und Person B, Login mit Benutzername und Passwort.
- Beide sehen alle gemeinsamen Einträge. Persönliche Einträge sind nur für die Person selbst sichtbar.
- Jeder Eintrag speichert, wer ihn angelegt hat.

## 6. Datenmodell (vereinfacht)

```
User
  - id, name, password_hash

Expense
  - id, amount, currency (EUR), date, merchant, description
  - category_id, owner: "A" | "B" | "SHARED"
  - receipt_image (optional, nur als Anhang)
  - created_by, created_at, updated_at

CalendarEvent
  - id, title, start, end, all_day, location, notes
  - owner: "A" | "B" | "SHARED"
  - recurrence (optional), reminder_minutes_before (optional)
  - created_by, created_at, updated_at

Task
  - id, title, notes, due_date, due_time (optional)
  - owner: "A" | "B" | "SHARED"
  - done, done_at, linked_event_id (optional)
  - reminder_at (optional)
  - created_by, created_at, updated_at

Category
  - id, name, icon, color

MerchantRule
  - merchant_name, category_id, default_owner

PushSubscription
  - id, user_id, endpoint, keys, created_at

NotificationSetting
  - user_id, type, enabled
```

## 7. Technische Umsetzung

| Bereich | Empfehlung |
|---|---|
| Frontend | React oder Next.js als PWA (responsive, installierbar) |
| Backend | Node.js (Express oder Fastify) oder Python (FastAPI) |
| Datenbank | PostgreSQL oder Supabase (eigenständig, ohne NAS) |
| Dateispeicher | Supabase Storage oder S3-kompatibler Speicher (für Belegfotos) |
| Push | Web Push (VAPID) über den Service Worker der PWA |
| Erinnerungen | Backend-Job (Cron), der fällige Erinnerungen an Push-Abos sendet |
| Belegfotos | Nur Anhang, Speicherung im Dateispeicher, keine Auslesung |
| Login | Passwort-Hash mit Session oder Auth-Dienst (z. B. Supabase Auth) |
| Hosting | Vercel (Frontend) plus Supabase oder Railway (Backend und Datenbank) |

**Hinweis Push auf dem iPhone:** Web-Push funktioniert nur, wenn die Web-App zum Home-Bildschirm hinzugefügt wurde (iOS 16.4 oder neuer). Ohne Installation kommen keine Push-Nachrichten.

**Hinweis Push auf dem PC:** Benachrichtigungen kommen zuverlässig, wenn die App als PWA installiert ist oder der Browser im Hintergrund läuft.

## 8. Bildschirme

1. **Login**
2. **Start:** heute, anstehende Termine, offene Aufgaben, letzte Ausgaben
3. **Kalender:** Tag, Woche, Monat, Filter nach Person
4. **Termin anlegen/bearbeiten**
5. **To-dos:** Tabs "Für mich", "Für uns", "Für [Person B]", erledigte Aufgaben ausblendbar
6. **Ausgaben:** Liste, Filter, Belegfoto aufnehmen
7. **Auswertung:** Jahresverlauf, Kategorien, Anteil nach Zahler
8. **Einstellungen:** Kategorien, Benachrichtigungen, Passwort, Export

## 9. Designgrundsätze

- Schnelle Eingabe: Termin, Aufgabe oder Ausgabe in höchstens 10 Sekunden.
- Zuordnung als drei große Schaltflächen: **A | B | Gemeinsam**.
- Farben: Person A, Person B, Gemeinsam jeweils eigene Farbe, überall gleich.
- Dunkel- und Hellmodus, Deutsch, Euro.

## 10. Umsetzungsphasen

**Phase 1 (MVP):**
- Login für zwei Personen
- Kalender mit Terminen (Zuordnung, Ansicht Tag/Woche/Monat)
- To-do-Liste mit Zuordnung
- Ausgaben manuell erfassen, Liste, Monatssumme
- Läuft als PWA, installierbar auf iPhone und PC

**Phase 2:**
- Push-Benachrichtigungen (Termine, Aufgaben)
- Wiederholungen bei Terminen
- Belegfotos als Anhang an Ausgaben
- Automatische Kategorisierung über Händlerregeln (ohne KI, lernt aus Korrekturen)

**Phase 3:**
- Jahresauswertung mit Diagrammen
- Export (CSV, PDF)
- Benachrichtigungseinstellungen pro Person
- Verknüpfung Aufgabe und Termin

## 11. Offene Fragen

1. Wo soll die App gehostet werden? Vorschlag: Supabase oder Railway (für zwei Nutzer meist sehr günstig, aber nicht immer ganz kostenlos).

## 12. Hinweis für die Umsetzung mit Claude Code

Gib Claude Code dieses Dokument als Kontext und starte mit Phase 1. Bitte zuerst Hosting und Datenbank festlegen, dann das Projektgerüst anlegen. Push-Funktionen brauchen HTTPS, daher ist eine Hosting-Lösung mit Zertifikat Pflicht.
