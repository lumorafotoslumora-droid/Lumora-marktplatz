# Lumora Marktplatz

Ein eigenständiger Produkt-Marktplatz (Node.js-Server, keine externen Pakete nötig) — wie Temu/eBay: Verkäufer stellen Produkte mit Titel, Beschreibung, Fotos und Preis ein, Käufer durchsuchen und kaufen sie mit echter Zahlung (Stripe).

Drei wichtige Unterschiede zum ursprünglichen Lumora:

1. **Jeder kann verkaufen.** Jedes Konto kann eigene Produkte einstellen (Titel, Beschreibung, bis zu 8 Fotos, Preis) und den eigenen Preis festlegen.
2. **Keine öffentliche Registrierung.** Die Seite ist öffentlich zugänglich (jeder kann browsen und kaufen), aber ein Konto kann **nur der Admin** erstellen — unter "Verwaltung" → "Konten".
3. **Kein Mitarbeiter-System mehr.** Es gibt nur noch zwei Rollen: **Admin** (verwaltet Aktionen, Gewinnspiel, Sperren, Support, Newsletter, Konten) und **Konto** (kann eigene Produkte einstellen/verkaufen und einkaufen).

Das Gewinnspiel existiert weiterhin und lässt sich vom Admin jederzeit unter "Verwaltung" → "Gewinnspiel" ein- und ausschalten. Es gibt **keine Versand-/Adresslogik** — Übergabe/Versand regeln Käufer und Verkäufer (bzw. Admin beim Gewinnspiel) direkt miteinander.

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

Ohne Stripe-Konfiguration (siehe unten) schlägt der Bezahlvorgang mit einer klaren Fehlermeldung fehl — bis auf Bestellungen, die durch eine 100%-Aktion (z.B. "Erstes Produkt gratis") komplett auf 0 € reduziert werden, die funktionieren immer auch ohne Stripe.

---

## Echte Zahlung mit Stripe einrichten

Die Website hat die Zahlungsanbindung an [Stripe](https://stripe.com) bereits fertig eingebaut (Kreditkarte und je nach Stripe-Konto auch weitere Methoden wie Klarna, Giropay etc.) — es fehlt nur dein eigener Stripe-Account und ein API-Schlüssel.

### Schritt für Schritt

1. Auf **https://dashboard.stripe.com/register** kostenlos ein Stripe-Konto erstellen (Name, E-Mail, Land — für Auszahlungen später zusätzlich Firmen-/Bankdaten nötig, zum Testen reicht die Registrierung).
2. Im Stripe-Dashboard oben rechts sicherstellen, dass **"Test mode"** aktiviert ist (Schalter oben rechts) — damit kannst du gefahrlos mit Test-Kreditkarten bezahlen, ohne dass echtes Geld fließt.
3. Im Menü links auf **"Developers" → "API keys"** gehen.
4. Den **"Secret key"** (beginnt mit `sk_test_...`) kopieren.
5. Diesen Key als Umgebungsvariable **`STRIPE_SECRET_KEY`** setzen:
   - **Lokal:** vor dem Start `export STRIPE_SECRET_KEY=sk_test_...` (Mac/Linux) bzw. `set STRIPE_SECRET_KEY=sk_test_...` (Windows), dann `node server.js`
   - **Auf Render.com:** im Dienst unter **"Environment"** → **"Add Environment Variable"** → Key `STRIPE_SECRET_KEY`, Value dein Schlüssel
6. Testen: Beim Checkout mit der Stripe-Testkarte **`4242 4242 4242 4242`**, beliebigem zukünftigem Ablaufdatum und beliebiger Prüfziffer bezahlen — die Bestellung wird wie eine echte Zahlung verarbeitet, es fließt aber kein echtes Geld.
7. **Wenn alles funktioniert:** Im Stripe-Dashboard oben rechts auf **"Live mode"** umschalten, dort unter "Developers → API keys" den **Live Secret Key** (beginnt mit `sk_live_...`) holen und `STRIPE_SECRET_KEY` damit ersetzen — ab da werden echte Zahlungen mit echtem Geld verarbeitet. Für Live-Zahlungen verlangt Stripe vorher deine Geschäfts-/Bankdaten (unter "Settings → Business details" im Dashboard).

Stripe zieht pro erfolgreicher Zahlung eine kleine Gebühr ab (in den meisten Ländern ca. 1,5–2,9% + einen Fixbetrag) — Details unter [stripe.com/pricing](https://stripe.com/pricing).

---

## Online veröffentlichen (kostenlos)

Empfehlung: **Render.com** (kostenlose Stufe reicht zum Testen).

1. Kostenloses Konto auf [render.com](https://render.com) erstellen
2. Dieses Repository verbinden: **New** → **Web Service** → Repository auswählen
3. Einstellungen:
   - **Build Command:** (leer lassen)
   - **Start Command:** `node server.js`
4. Umgebungsvariablen setzen:
   - **`STRIPE_SECRET_KEY`** — siehe Stripe-Anleitung oben (ohne diese Variable funktioniert keine echte Zahlung)
   - **`SITE_URL`** — die echte Adresse (z.B. `https://dein-name.onrender.com`), damit Links in automatischen E-Mails korrekt sind
   - **`BREVO_API_KEY`** und optional **`SENDER_EMAIL`** — für den E-Mail-Versand (Gewinnspiel, Newsletter, Support), siehe unten
   - **`SUPABASE_URL`** und **`SUPABASE_SERVICE_KEY`** — für dauerhaften Speicher, siehe unten (ohne diese Variablen gehen bei jedem Neu-Deploy alle Konten/Produkte verloren!)
5. **Create Web Service** klicken

---

## E-Mail-Versand mit Brevo einrichten

Für Gewinnspiel-, Newsletter- und Support-Mails nutzt die Seite [Brevo](https://brevo.com) (ehemals Sendinblue) — kostenlos bis 300 Mails/Tag.

1. Kostenlosen Account auf [app.brevo.com](https://app.brevo.com) erstellen
2. Profil-Symbol → **"SMTP & API"** → Tab **"API Keys"** → **"Generate a new API key"**
3. Den angezeigten Schlüssel (beginnt mit `xkeysib-...`) sofort kopieren — er wird nur einmal angezeigt
4. Als Umgebungsvariable **`BREVO_API_KEY`** setzen (lokal per `export`, auf Render unter "Environment")
5. Optional: **`SENDER_EMAIL`** auf eine bei Brevo unter "Senders" verifizierte Absender-Adresse setzen (sonst wird ein Standardwert genutzt)

---

## Dauerhafter Speicher mit Supabase (wichtig für kostenloses Hosting!)

Bei kostenlosen Hosting-Stufen wie Render Free gehen hochgeladene Dateien und die Datenbank bei jedem Neu-Deploy verloren, da der Speicherplatz nicht dauerhaft ist. Die Seite kann stattdessen [Supabase](https://supabase.com) nutzen (kostenlos bis 500MB Datenbank + 1GB Dateispeicher) — dann bleiben Konten und Produkte auch über Deploys hinweg erhalten.

1. Kostenlosen Account auf [supabase.com](https://supabase.com) erstellen, neues Projekt anlegen
2. Im Projekt unter **"Storage"** zwei Buckets anlegen:
   - **`db`** — **privat** (Public bucket NICHT aktivieren)
   - **`uploads`** — **öffentlich** (Public bucket aktivieren)
3. Unter **"API Keys"** (bzw. Project Settings → API) kopieren:
   - **Project URL**
   - **service_role key** (geheim! niemals öffentlich teilen — hat vollen Datenbankzugriff)
4. Als Umgebungsvariablen setzen:
   - **`SUPABASE_URL`** = die Project URL
   - **`SUPABASE_SERVICE_KEY`** = der service_role key

Ohne diese beiden Variablen läuft der Server automatisch im lokalen Datei-Modus weiter (z.B. praktisch für Tests auf dem eigenen Computer) — auf Render bedeutet das aber Datenverlust bei jedem Deploy.

---

## Rollen & Rechte

| Funktion | Admin | Konto (Verkäufer/Käufer) |
|---|---|---|
| Produkte einstellen & verkaufen | ✅ | ✅ (nur eigene) |
| Preis eigener Produkte ändern | ✅ | ✅ (nur eigene) |
| Eigene Verkäufe einsehen | ✅ (alle) | ✅ (nur eigene) |
| Konten erstellen/löschen | ✅ | ❌ |
| Aktionen (Rabatte) verwalten | ✅ | ❌ |
| Gewinnspiel ein/aus schalten, Gewinner auslosen | ✅ | ❌ |
| Konten sperren/entsperren | ✅ | ❌ |
| Support-Nachrichten & Newsletter verwalten | ✅ | ❌ |
| Website-Frontend live aktualisieren | ✅ | ❌ |

Es gibt **keine öffentliche Registrierung** — der `/api/register`-Endpunkt existiert absichtlich nicht mehr. Konten werden ausschließlich über `/api/accounts` (nur Admin) angelegt.

## Wie ein Verkauf abläuft

1. Ein Konto stellt unter "Verwaltung" ein Produkt ein (Titel, Beschreibung, 1–8 Fotos, Preis).
2. Andere Konten sehen es in der Galerie, legen es in den Warenkorb und bezahlen über Stripe.
3. Nach erfolgreicher Zahlung gilt das Produkt als **verkauft** (`sold`) und verschwindet aus dem aktiven Angebot der anderen — es ist ja wie bei einem einzelnen physischen Artikel nur einmal vorhanden.
4. Übergabe/Versand klären Käufer und Verkäufer direkt (z.B. über die im Konto hinterlegte E-Mail) — die Website bildet das nicht automatisch ab.
