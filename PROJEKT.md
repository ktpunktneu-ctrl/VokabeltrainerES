# Vokabeltrainer — Projektübersicht

Stand: 2026-07-14

## Was ist das

Vier eigenständige, baugleiche PWA-Vokabeltrainer-Apps von Klaus Tegtmeier:

| | VokabeltrainerIT | VokabeltrainerEN | VokabeltrainerFR | VokabeltrainerES |
|---|---|---|---|---|
| Sprachpaar | Italienisch ↔ Deutsch | Englisch ↔ Deutsch | Französisch ↔ Deutsch | Spanisch ↔ Deutsch |
| Verzeichnis | `C:\Users\klaus\VokabeltrainerIT` | `C:\Users\klaus\VokabeltrainerEN` | `C:\Users\klaus\VokabeltrainerFR` | `C:\Users\klaus\VokabeltrainerES` |
| Port (lokal) | 5052 | 5053 | 5054 | 5055 |
| Live-URL | https://ktpunktneu-ctrl.github.io/VokabeltrainerIT/ | https://ktpunktneu-ctrl.github.io/VokabeltrainerEN/ | https://ktpunktneu-ctrl.github.io/VokabeltrainerFR/ | https://ktpunktneu-ctrl.github.io/VokabeltrainerES/ |
| GitHub-Repo | ktpunktneu-ctrl/VokabeltrainerIT | ktpunktneu-ctrl/VokabeltrainerEN | ktpunktneu-ctrl/VokabeltrainerFR | ktpunktneu-ctrl/VokabeltrainerES |
| Vokabelanzahl | 548 (9 Kategorien) | 539 (9 Kategorien) | 539 (9 Kategorien) | 539 (9 Kategorien) |
| App-Version | v1.9 | v1.9 | v1.9 | v1.9 |
| OCR-Sprachcode | ita+deu | eng+deu | fra+deu | spa+deu |

## Technik

- **Backend:** Flask (`main.py`), liefert `index.html` (Cache-Control: no-store) und die Endpoints `/api/vokabeln` (GET/POST/PUT/DELETE), `/api/kategorien` und `/api/ocr` (Foto → Text via Tesseract-OCR, Sprachdaten in `tessdata/`). Persistenz serverseitig in `vokabeln.json` — kennt nur `id/kategorie/it/de` (Feldname `it` ist historisch aus dem IT-Projekt übernommen und wird aus Kompatibilitätsgründen in allen vier Apps unverändert als Schlüssel für das Fremdwort verwendet, unabhängig von der tatsächlichen Sprache). Läuft nur lokal, nicht Teil des Pages-Deployments.
- **Frontend:** Eine einzige `index.html` (HTML+CSS+JS inline). Daten primär in `localStorage` (Key `vokabeltrainer_{it,en,fr,es}_vokabeln`), mit `SEED_VOKABELN` als Fallback/Erstbefüllung — dort sind auch die Konjugationsformen (`formen`) hinterlegt.
- **PWA/Offline:** Service Worker (`static/sw.js` bzw. `docs/sw.js`) cached die App-Shell cache-first, funktioniert komplett offline unterwegs. **Wichtig:** Bei jeder inhaltlichen Änderung an `index.html` muss die `CACHE`-Versionskonstante in **beiden** `sw.js`-Kopien (`static/` und `docs/`) hochgezählt werden, sonst bleibt die alte Version für immer aktiv.
- **Pfade:** Alle Ressourcen-Pfade (`manifest.json`, Icons, `sw.js`-Registrierung) sind **relativ** (kein führendes `/`) — funktioniert dadurch sowohl lokal via Flask (an der Domain-Wurzel) als auch auf GitHub Pages (im jeweiligen Unterpfad).
- **`docs/`-Ordner** ist bei allen Apps 1:1-Kopie von Root (`index.html`, `sw.js` mit relativen Pfaden, eigenes `manifest.json`) für GitHub Pages Deployment. Bei jeder Änderung an Root-Dateien `docs/` manuell synchron halten — Pages baut automatisch bei jedem Push nach `main`.
- **Sprachmodul:** Web Speech API (`speechSynthesis`), kostenlos/offline, 🔊-Button im Quiz und in der Lernliste.
- **Konjugations-/Verbformentraining:** eigener Modus — fragt zufällige Personalform ab, nur für Vokabeln mit `formen`-Feld. Teil der Vollversion.
- **Lernliste:** eigener Kategorie-Filter (isoliert bei Klick, wie im Quiz-Start) + Richtungsumschalter, unabhängig vom Quiz-Modus, aber gleiche `S.selectedKat`-Auswahl geteilt mit dem Quiz-Start. Eigenes Suchfeld, hat Vorrang vor Kategorie-Filter.
- **Verwaltung:** Neue Vokabel (Live-Duplikat-Warnung), neue Kategorie (eigenes Modal), Vokabeln aus Foto per OCR (Kamera/Datei-Upload → Tesseract → editierbare Kandidatenliste → Bulk-Übernahme in eine Kategorie).
- **Trial-Modell:** Ohne Freischaltung ist Training/Quiz auf 10 Vokabeln je Kategorie begrenzt (Lernliste bleibt komplett sichtbar), Speichern neuer Vokabeln/Kategorien/OCR-Ergebnisse gesperrt (Lizenz-Modal öffnet sich sofort beim Öffnen des jeweiligen Formulars). Nach 14 Tagen ohne Freischaltung öffnet sich das Lizenz-Modal automatisch bei jedem Start.

## Änderungswarnung

Alle vier Apps sind komplett parallel gepflegt — **jede Code-Änderung muss identisch in allen vier Projekten nachgezogen werden.**

## Changelog (Auszug, chronologisch)

- **2026-07-04:** Sprachmodul/TTS ergänzt, Konjugationsformen-Sync-Bug behoben, Kategorie-Filter im Quiz auf "isolieren statt togglen" umgestellt, Kategorie-Löschung inkl. enthaltener Vokabeln, sichtbare Versionsnummer im Header.
- **2026-07-05:** Je 50 neue Vokabeln pro Kategorie ergänzt (→ 539 gesamt je Sprache), absolute Pfade auf relative umgestellt (GitHub-Pages-Subpath-Bug behoben), Lernliste-Kategoriefilter auf "isolieren" umgestellt.
- **2026-07-06:** Hilfe-Button (❓) mit Kurzanleitung ergänzt, Suchfunktion in Lernliste + Verwaltung, echtes Auto-Update (aktive Prüfung + Auto-Reload), Feature-Gating für Konjugation/Verbformen implementiert (siehe Vermarktungs-Abschnitt).
- **2026-07-11/12 (zuerst nur IT, v1.5–v1.9):** Hilfe-Modal überarbeitet, Add-Modal umgebaut (Reihenfolge Fremdwort → Duplikat-Warnung → Deutsch → Kategorie, merkt sich letzte Auswahl), eigenes Kategorie-Modal, Header-Icon durch "⋯"-Overflow-Menü ersetzt, neues Trial-Modell (10 Vokabeln/Kategorie im Test), OCR-Feature "📷 Vokabeln aus Foto".
- **2026-07-14:** EN auf v1.9 nachgezogen (Feature-Parität mit IT hergestellt). VokabeltrainerFR und VokabeltrainerES neu angelegt (gleicher Featurestand v1.9 von Anfang an), Vokabular jeweils übersetzt (Basis: EN-Wortschatz), `fra.traineddata`/`spa.traineddata` für OCR ergänzt.

## Vermarktung — Feature-Gating (seit 2026-07-06, Trial-Modell seit 2026-07-12)

Zielgruppe: Schüler & Interessierte, "schmaler Kurs" (kleiner, günstiger Zugang statt Vollpreis-Produkt).

- **Freischaltung:** Code-Eingabefeld im Lizenz-Modal, prüft per Fetch gegen Gumroads License-Verification-API (`https://api.gumroad.com/v2/licenses/verify`).
- **Preis:** 9,95 € einmalig, Kauf-Link im Modal.
- **Master-Code für Eigennutzung:** `ktpunkt-master-2026` — schaltet sofort und dauerhaft frei, ganz ohne Gumroad-Prüfung/Internet. Konstante `MASTER_CODE` in `index.html` (alle vier Apps).
- **Bestandsnutzer-Schutz:** `pruefeBestandsnutzer()` schaltet Nutzer, die vor dem Feature-Gating schon Daten hatten, automatisch dauerhaft frei.

**Noch offen / ACHTUNG — Klaus muss selbst tun:**
- **Gumroad-Produkte existieren noch nicht!** `GUMROAD_PERMALINK` in `index.html` ist je Sprache nur ein Platzhalter (`vokabeltrainer-{it,en,fr,es}`), ebenso der Kauf-Link im Lizenz-Modal. Vier separate Gumroad-Produkte anlegen (Preis 9,95 €, License-Key-Generierung aktivieren), dann echte Permalinks eintragen (Stelle mit `// TODO Klaus:` markiert).

## Schnellzugriff

- IT lokal starten: `start_vokabeltrainer.bat` (Port 5052)
- EN lokal starten: `start_vokabeltrainer_en.bat` (Port 5053)
- FR lokal starten: `start_vokabeltrainer_fr.bat` (Port 5054)
- ES lokal starten: `start_vokabeltrainer_es.bat` (Port 5055)
- IT live: https://ktpunktneu-ctrl.github.io/VokabeltrainerIT/
- EN live: https://ktpunktneu-ctrl.github.io/VokabeltrainerEN/
- FR live: https://ktpunktneu-ctrl.github.io/VokabeltrainerFR/
- ES live: https://ktpunktneu-ctrl.github.io/VokabeltrainerES/
