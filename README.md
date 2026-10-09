# Angel Parkinglot

Zahlungsübersicht für die drei Parkanlagen: pro Monat sieht man, wer **bezahlt** (Pagado / Paid), **nicht bezahlt** (No pagado / Unpaid) oder **uneindeutig** (Dudoso / Unclear) ist. Läuft als eine einzige HTML-Seite im Browser – auf dem PC und auf dem iPhone.

Die Oberfläche gibt es auf **Spanisch und Englisch**; umgeschaltet wird mit dem Knopf **ES | EN** oben rechts. Die Wahl wird pro Gerät gemerkt.

**App öffnen:** https://lucashcandido.github.io/Angel-parkinglot/

## Was die App kann

- Parkanlage per Klick wählen (oder „Alle“), Namen der Anlagen frei änderbar
- Monatsansicht mit den drei Listen, Blättern zu früheren und späteren Monaten
- Ein Tipp auf ✓ / ? / ✕ ändert den Status, mit „Deshacer / Undo“ zum Zurücknehmen
- **Kennzeichen** (Patente / Licence plate) pro Kunde: steht als Schild direkt neben dem Namen, ist durchsuchbar (auch ohne Leerzeichen) und sortierbar; mehrere Kennzeichen mit Komma trennen
- Pro Kunde hinterlegbar, **über welche anderen Namen er zahlt** – die Suche findet ihn dann auch über den Namen auf dem Kontoauszug
- **Ein Klick auf den Kunden** öffnet ein einziges Fenster für alles: Zahlung des Monats (Status, Betrag, Datum, „überwiesen von“, Notiz), Status früherer Monate und sämtliche Kundendaten – gespeichert wird mit einem Knopf
- Ein neuer Zahlername lässt sich dabei mit einem Haken dauerhaft merken
- Hinweis, wenn bei einem Kunden noch Vormonate offen sind
- Kundenliste direkt aus Excel einfügen, Monat als CSV exportieren, Sicherung als Datei

## Kontoauszüge mit Claude einlesen

Über **Extracto bancario / Bank statement** lädt man ein PDF oder Screenshots der Bank hoch. Claude liest die Zahlungseingänge und ordnet sie den Kunden zu. Vor dem Speichern zeigt die App eine Vorschau, in der jede Zuordnung geändert werden kann:

- Name passt eindeutig und der Betrag reicht → Vorschlag **bezahlt**
- Name passt nur ungefähr oder der Betrag ist zu niedrig → Vorschlag **uneindeutig**, Claudes Begründung wird als Notiz gespeichert
- kein passender Kunde, Datum außerhalb des Monats oder schon einmal eingelesen → wird nicht eingetragen, lässt sich aber von Hand zuordnen

Dafür braucht die App einen eigenen **Anthropic-API-Schlüssel** (https://platform.claude.com/settings/keys, Guthaben unter „Billing“). Die Abrechnung läuft nach Verbrauch über das Anthropic-Konto. Der Schlüssel wird nur im Browser des Geräts gespeichert; der QR-Code für ein weiteres Gerät überträgt ihn mit. Beim Einlesen gehen die Dateien und die Kundenliste (Namen, Zahlernamen, Beträge) an Anthropic, sonst nirgendwohin.

## Speicherung auf mehreren Geräten

Dieses Repo enthält **nur den Programmcode**. Die Kundendaten liegen getrennt im privaten Repo `Angel-parkinglot-data` (Datei `data.json`) und werden von der App automatisch gelesen und geschrieben. Ändern zwei Geräte gleichzeitig etwas, werden die Stände zusammengeführt; pro Kunde und Monat gilt die jeweils neueste Änderung. Ohne Internet speichert die App lokal und gleicht später ab.

### Einmalig einrichten

1. Token erstellen: https://github.com/settings/personal-access-tokens/new
   - **Repository access:** „Only select repositories“ → `Angel-parkinglot-data`
   - **Permissions → Repository permissions → Contents:** „Read and write“
   - **Expiration:** „No expiration“ oder das längste Angebot
2. App öffnen → Zahnrad → **Sincronización entre dispositivos / Device sync** → Token einfügen → **Conectar / Connect**.
3. Weiteres Gerät im Browser: auf dem ersten Gerät **Conectar otro dispositivo / Connect another device** wählen und den QR-Code mit der Kamera scannen.

### Als App auf dem iPhone (Home-Bildschirm)

1. Die Adresse der App auf dem iPhone in **Safari** öffnen.
2. Teilen-Symbol → **„Zum Home-Bildschirm“** → „Hinzufügen“.
3. Die App über das neue Symbol öffnen. Sie hat einen eigenen, von Safari getrennten Speicher und ist deshalb zunächst nicht verbunden.
4. In der App: Zahnrad → **Escanear código QR / Scan QR code** und den QR-Code scannen, den der PC unter „Conectar otro dispositivo“ zeigt. Alternativ den Verbindungs-Link einfügen.

Die App startet danach auch ohne Internet (die Daten des letzten Abgleichs bleiben sichtbar, Änderungen werden später synchronisiert).

Der Token wird nur im Browser des jeweiligen Geräts gespeichert und nur an `api.github.com` gesendet. Den Kopplungs-Link bzw. QR-Code nicht weitergeben – er enthält den Token.

## Technik

- `index.html` – die komplette App (HTML, CSS, JavaScript), keine externen Abhängigkeiten zur Laufzeit
- `sw.js` – hält die App-Dateien für den Start ohne Internet vor (Netz zuerst, gespeicherte Kopie als Rückfall)
- `vendor/jsQR.js` – QR-Code-Erkennung mit der Kamera ([jsQR](https://github.com/cozmo/jsQR) 1.4.0, Apache-2.0-Lizenz)
- `vendor/qrcode.js` – QR-Code-Erzeugung ([qrcode-generator](https://github.com/kazuhikoarase/qrcode-generator) 1.4.4, MIT-Lizenz, © Kazuhiko Arase)
- `manifest.webmanifest`, `icon-*.png` – Symbol für den Home-Bildschirm
