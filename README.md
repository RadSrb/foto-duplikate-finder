# Foto-Duplikate finden

Eine kleine Web-App, die exakte (bit-identische) Foto-Duplikate findet — komplett lokal im Browser, ohne Upload.

- Fotos auswählen → SHA-256-Vergleich im Browser
- Gruppierung nach Dateigröße, dann Hash
- Aufnahmedatum aus EXIF (JPEG), sonst Dateidatum
- Geführte Checkliste zum Löschen in der Fotos-App

Statische Single-File-App: `index.html`, keine Abhängigkeiten, kein Build.

---

Deployed via Vercel.
