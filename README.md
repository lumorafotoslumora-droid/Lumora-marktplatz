# Lumora Marktplatz

Ein eigenständiger Bilder-Marktplatz (Node.js-Server, keine externen Pakete nötig) — abgeleitet von Lumora, mit drei wichtigen Unterschieden:

1. **Jeder kann verkaufen.** Nicht mehr nur Admin/Mitarbeiter dürfen Bilder hochladen — jedes Konto kann eigene Bilder hochladen, den eigenen Preis festlegen und verkaufen.
2. **Keine öffentliche Registrierung.** Die Seite ist öffentlich zugänglich (jeder kann browsen und kaufen), aber ein Konto kann **nur der Admin** erstellen — unter "Verwaltung" → "Konten".
3. **Kein Mitarbeiter-System mehr.** Es gibt nur noch zwei Rollen: **Admin** (verwaltet Aktionen, Gewinnspiel, Sperren, Support, Newsletter, Konten) und **Konto** (kann eigene Bilder hochladen/verkaufen und einkaufen).

Das Gewinnspiel existiert weiterhin und lässt sich vom Admin jederzeit unter "Verwaltung" → "Gewinnspiel" ein- und ausschalten.

---

## Lokal testen

1. [Node.js](https://nodejs.org) installieren
2. Im Projektordner:
   ```
   node server.js
   ```
3. Im Browser öffnen: **http://localhost:3000**

Admin-Zugang beim ersten Start: Name `Matic`, Passwort `VIP` (unter "Mein Konto" → "Passwort ändern" anpassbar).

Alle weiteren Konten (egal ob zum Verkaufen oder nur zum Einkaufen) legst du als Admin unter **Verwaltung → Konten** an.

---

## Online veröffentlichen (kostenlos)

Empfehlung: **Render.com** (kostenlose Stufe reicht zum Testen).

1. Kostenloses Konto auf [render.com](https://render.com) erstellen
2. Dieses Repository verbinden: **New** → **Web Service** → Repository auswählen
3. Einstellungen:
   - **Build Command:** (leer lassen)
   - **Start Command:** `node server.js`
4. Optional, aber empfohlen: Umgebungsvariable **`SITE_URL`** auf die echte Adresse setzen (z.B. `https://dein-name.onrender.com`), damit Links in automatischen E-Mails korrekt sind.
5. **Create Web Service** klicken

### Wichtiger Hinweis zu kostenlosem Hosting

Bei kostenlosen Hosting-Stufen werden hochgeladene Dateien und die Datenbank-Datei bei einem Neustart des Servers gelöscht, da der Speicherplatz nicht dauerhaft ist. Für Dauerbetrieb: bezahlter Plan mit **Persistent Disk**, oder externe Speicherlösung (z.B. Cloudflare R2, AWS S3).

---

## Rollen & Rechte

| Funktion | Admin | Konto (Verkäufer/Kunde) |
|---|---|---|
| Bilder hochladen & verkaufen | ✅ | ✅ (nur eigene) |
| Preis/Gratis-Status eigener Bilder ändern | ✅ | ✅ (nur eigene) |
| Eigene Verkäufe einsehen | ✅ (alle) | ✅ (nur eigene) |
| Konten erstellen/löschen | ✅ | ❌ |
| Aktionen (Rabatte) verwalten | ✅ | ❌ |
| Gewinnspiel ein/aus schalten, Gewinner auslosen | ✅ | ❌ |
| Konten sperren/entsperren | ✅ | ❌ |
| Support-Nachrichten & Newsletter verwalten | ✅ | ❌ |
| Website-Frontend live aktualisieren | ✅ | ❌ |

Es gibt **keine öffentliche Registrierung** — der `/api/register`-Endpunkt existiert absichtlich nicht mehr. Konten werden ausschließlich über `/api/accounts` (nur Admin) angelegt.
