# Zwei-Personen-App

Gemeinsame, eigenständige Web-App (PWA) für zwei Personen:
Ausgaben, Kalender und To-do-Listen, mit Push-Benachrichtigungen auf iPhone und Windows-PC.

📄 **Konzept:** [KONZEPT.md](./KONZEPT.md)

## Status

Phase 0 – Konzept liegt vor. Das Projektgerüst (Phase 1, MVP) ist noch nicht angelegt.

## Offene Entscheidungen

Laut Konzept (Abschnitt 11 und 12) müssen vor dem Projektgerüst zuerst festgelegt werden:

- [ ] **Hosting:** Vorschlag Supabase (Datenbank, Auth, Storage) mit Vercel (Frontend)
- [ ] **Frontend:** Vorschlag React oder Next.js als PWA
- [ ] **Datenbank:** Vorschlag PostgreSQL bzw. Supabase
- [ ] **Login:** Vorschlag Supabase Auth mit zwei festen Konten

## Geplante Struktur

Noch nicht angelegt. Sie entsteht, sobald die offenen Entscheidungen getroffen sind:

```
zwei-personen-app/
├── KONZEPT.md     # Anforderungen und Datenmodell
├── README.md      # dieser Überblick
└── …              # Projektcode (Phase 1)
```

Push-Benachrichtigungen brauchen HTTPS. Die Hosting-Lösung muss daher ein Zertifikat bereitstellen.
