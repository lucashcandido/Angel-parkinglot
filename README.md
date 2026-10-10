# Parkinglot Manager

Zahlungsübersicht für die drei Parkanlagen: pro Monat sieht man, wer **bezahlt** (Pagado / Paid), **nicht bezahlt** (No pagado / Unpaid) oder **uneindeutig** (Dudoso / Unclear) ist. Läuft als eine einzige HTML-Seite im Browser – auf dem PC und auf dem iPhone.

Die Oberfläche gibt es auf **Spanisch und Englisch**; umgeschaltet wird mit dem Knopf **ES | EN** oben rechts. Die Wahl wird pro Gerät gemerkt.

**App öffnen:** https://lucashcandido.github.io/Angel-parkinglot/

## Was die App kann

- Parkanlage per Klick wählen (oder „Alle“), Namen der Anlagen frei änderbar
- Monatsansicht mit den drei Listen, Blättern zu früheren und späteren Monaten
- Ein Tipp auf ✓ / ? / ✕ ändert den Status, mit „Deshacer / Undo“ zum Zurücknehmen
- **Zahlungserinnerung per WhatsApp:** Bei jedem offenen Kunden mit Telefonnummer öffnet ein Knopf direkt dessen WhatsApp-Chat mit einem fertigen spanischen Text (Name, Betrag, offene Monate). Der Text ist in den Einstellungen anpassbar; die App merkt sich den Tag der letzten Erinnerung
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

Dafür braucht die App einen eigenen **Anthropic-API-Schlüssel** (https://platform.claude.com/settings/keys, Guthaben unter „Billing“). Die Abrechnung läuft nach Verbrauch über das Anthropic-Konto.

Der Schlüssel wird einmal eingetragen und zusammen mit den Daten abgeglichen: Er liegt in `data.json` im privaten Daten-Repo und kommt so von selbst auf alle verbundenen Geräte, ebenso die Modellwahl. Entfernen wirkt genauso auf allen Geräten. In Sicherungsdateien steht er nicht. Weil er im Verlauf des Repos erhalten bleibt, sollte ein nicht mehr benutzter Schlüssel auch in der Anthropic-Konsole widerrufen werden – und das Daten-Repo muss privat bleiben.

Beim Einlesen gehen die Dateien und die Kundenliste (Namen, Zahlernamen, Beträge) an Anthropic, sonst nirgendwohin.

## Speicherung auf mehreren Geräten

Dieses Repo enthält **nur den Programmcode**. Die Kundendaten liegen getrennt im privaten Repo `Angel-parkinglot-data` (Datei `data.json`) und werden von der App automatisch gelesen und geschrieben. Ändern zwei Geräte gleichzeitig etwas, werden die Stände zusammengeführt; pro Kunde und Monat gilt die jeweils neueste Änderung. Ohne Internet speichert die App lokal und gleicht später ab.

### Einmalig einrichten (Administrator)

1. Token erstellen: https://github.com/settings/personal-access-tokens/new
   - **Repository access:** „Only select repositories“ → **beide** auswählen: `Angel-parkinglot-data` und `Angel-parkinglot`
   - **Permissions → Repository permissions → Contents:** „Read and write“
   - **Expiration:** „No expiration“ oder das längste Angebot
2. App öffnen → **Primera configuración (administrador) / First-time setup** → Token einfügen → **Conectar / Connect**.
3. Die App erzeugt daraufhin von selbst einen **Zugangscode** (z. B. `WK6B-4APP-D4CW`) und zeigt ihn in den Einstellungen an.

### Jedes weitere Gerät

App öffnen, den Zugangscode einmal eintippen, **Entrar / Enter** – fertig. Alternativ den Link aus den Einstellungen („Copiar enlace“) schicken, z. B. per WhatsApp: Antippen verbindet das Gerät ohne Tippen. Kein Token, kein QR-Code, keine GitHub-Kenntnisse nötig.

So funktioniert es: Die App verschlüsselt Repo-Name und Token mit dem Zugangscode (PBKDF2-SHA256 mit 600 000 Runden, AES-GCM) und legt das Ergebnis als `access.json` in dieses Repo. Ein neues Gerät lädt die Datei, entschlüsselt sie mit dem eingetippten Code und ist verbunden. Ohne den Code ist die Datei wertlos; der Code selbst ist zufällig erzeugt (12 Zeichen) und steht nur in den privaten Daten.

- **Token läuft ab oder wird ersetzt:** Der Administrator trägt den neuen Token ein (Einstellungen → Desconectar, dann wie oben). Der Zugangscode bleibt gleich, und alle anderen Geräte holen sich den neuen Token beim nächsten Abgleich von selbst.
- **Neuer Zugangscode:** „Generar código nuevo“ – der alte Code gilt dann nicht mehr für neue Geräte; verbundene Geräte laufen weiter.
- **Gerät abmelden:** „Desconectar“ auf dem Gerät. Um ein verlorenes Gerät auszusperren, den Token bei GitHub löschen, einen neuen eintragen und einen neuen Code erzeugen.

### Als App auf dem iPhone (Home-Bildschirm)

1. Die Adresse der App auf dem iPhone in **Safari** öffnen.
2. Teilen-Symbol → **„Zum Home-Bildschirm“** → „Hinzufügen“.
3. Die App über das neue Symbol öffnen. Fragt sie nach dem Zugangscode, ihn dort einmal eintippen (die App vom Home-Bildschirm hat einen eigenen, von Safari getrennten Speicher).

Die App startet danach auch ohne Internet (die Daten des letzten Abgleichs bleiben sichtbar, Änderungen werden später synchronisiert).

Der Token wird nur an `api.github.com` gesendet.

## Technik

- `index.html` – die komplette App (HTML, CSS, JavaScript), keine externen Abhängigkeiten zur Laufzeit
- `access.json` – verschlüsselter Zugang für weitere Geräte; wird von der App geschrieben, nicht von Hand ändern
- `sw.js` – hält die App-Dateien für den Start ohne Internet vor (Netz zuerst, gespeicherte Kopie als Rückfall)
- `manifest.webmanifest`, `icon-*.png` – Symbol für den Home-Bildschirm
