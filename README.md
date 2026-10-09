# Angel Parkinglot

Zahlungsübersicht für die drei Parkanlagen: pro Monat sieht man, wer **bezahlt** (Pagado / Paid), **nicht bezahlt** (No pagado / Unpaid) oder **uneindeutig** (Dudoso / Unclear) ist. Läuft als eine einzige HTML-Seite im Browser – auf dem PC und auf dem iPhone.

Die Oberfläche gibt es auf **Spanisch und Englisch**; umgeschaltet wird mit dem Knopf **ES | EN** oben rechts. Die Wahl wird pro Gerät gemerkt.

**App öffnen:** https://lucashcandido.github.io/Angel-parkinglot/

## Was die App kann

- Parkanlage per Klick wählen (oder „Alle“), Namen der Anlagen frei änderbar
- Monatsansicht mit den drei Listen, Blättern zu früheren und späteren Monaten
- Ein Tipp auf ✓ / ? / ✕ ändert den Status, mit „Deshacer / Undo“ zum Zurücknehmen
- Pro Kunde hinterlegbar, **über welche anderen Namen er zahlt** – die Suche findet ihn dann auch über den Namen auf dem Kontoauszug
- **Ein Klick auf den Kunden** öffnet ein einziges Fenster für alles: Zahlung des Monats (Status, Betrag, Datum, „überwiesen von“, Notiz), Status früherer Monate und sämtliche Kundendaten – gespeichert wird mit einem Knopf
- Ein neuer Zahlername lässt sich dabei mit einem Haken dauerhaft merken
- Hinweis, wenn bei einem Kunden noch Vormonate offen sind
- Kundenliste direkt aus Excel einfügen, Monat als CSV exportieren, Sicherung als Datei

## Speicherung auf mehreren Geräten

Dieses Repo enthält **nur den Programmcode**. Die Kundendaten liegen getrennt im privaten Repo `Angel-parkinglot-data` (Datei `data.json`) und werden von der App automatisch gelesen und geschrieben. Ändern zwei Geräte gleichzeitig etwas, werden die Stände zusammengeführt; pro Kunde und Monat gilt die jeweils neueste Änderung. Ohne Internet speichert die App lokal und gleicht später ab.

### Einmalig einrichten

1. Token erstellen: https://github.com/settings/personal-access-tokens/new
   - **Repository access:** „Only select repositories“ → `Angel-parkinglot-data`
   - **Permissions → Repository permissions → Contents:** „Read and write“
   - **Expiration:** „No expiration“ oder das längste Angebot
2. App öffnen → Zahnrad → **Sincronización entre dispositivos / Device sync** → Token einfügen → **Conectar / Connect**.
3. Zweites Gerät: auf dem ersten Gerät **Conectar otro dispositivo / Connect another device** wählen und den QR-Code mit der iPhone-Kamera scannen.
4. Auf dem iPhone in Safari: Teilen → **„Zum Home-Bildschirm“**, dann startet die App wie eine normale App.

Der Token wird nur im Browser des jeweiligen Geräts gespeichert und nur an `api.github.com` gesendet. Den Kopplungs-Link bzw. QR-Code nicht weitergeben – er enthält den Token.

## Technik

- `index.html` – die komplette App (HTML, CSS, JavaScript), keine externen Abhängigkeiten zur Laufzeit
- `vendor/qrcode.js` – QR-Code-Erzeugung ([qrcode-generator](https://github.com/kazuhikoarase/qrcode-generator) 1.4.4, MIT-Lizenz, © Kazuhiko Arase)
- `manifest.webmanifest`, `icon-*.png` – Symbol für den Home-Bildschirm
