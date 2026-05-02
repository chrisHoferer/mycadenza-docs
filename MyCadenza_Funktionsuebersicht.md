# MyCadenza – Funktionsübersicht

> Dokumentation der App-Funktionsweise für den Entwickler.
> Stand: 02.05.2026 · v1.7.0

---

## 1. Was MyCadenza macht

MyCadenza ist ein persönlicher Tagesplaner für iPhone und iPad. Die App organisiert wiederkehrende Aufgaben mit Teilaufgaben, Kategorien und akustischem Feedback. Der Fokus liegt auf dem aktuellen Tag: Was ist fällig, was kommt noch, was ist erledigt?

**Kernidee:** Jede Aufgabe hat eine Startzeit und eine Gültigkeitsdauer. Ist die Zeit abgelaufen, bleibt die Aufgabe für eine konfigurierbare **Verfallfrist** noch offen — der Nutzer kann sie nachholen. Erst danach wird sie automatisch als übersprungen markiert und die nächste Wiederholung erstellt. Der Nutzer muss sich nicht um Planung kümmern — die App erledigt das.

---

## 2. Die vier Hauptbereiche (Tabs)

### 2.1 Heute

Die zentrale Ansicht. Zeigt alle Aufgaben des aktuellen Tages, aufgeteilt in fünf Sektionen:

| Sektion | Inhalt | Initial |
|---|---|---|
| **Überfällig** | Aufgaben, deren Gültigkeitsdauer abgelaufen ist, die aber innerhalb der Verfallfrist noch offen sind | Offen |
| **Fällig** | Aufgaben, die jetzt bearbeitet werden können (Startzeit erreicht, noch nicht abgelaufen) | Offen |
| **Anstehend** | Aufgaben für heute, deren Startzeit noch nicht erreicht ist | Offen |
| **Ausblick** | Aufgaben für die kommenden Tage, nach Datum gruppiert (Morgen, Übermorgen, ...) | Geschlossen |
| **Abgeschlossen** | Erledigte und übersprungene Aufgaben des Tages | Geschlossen |

Jede Sektion zeigt ihre Aufgaben nach Kategorien gruppiert. Jede Kategorie ist einzeln auf-/zuklappbar.

**Bedienung:**
- Tap auf eine Aufgabe → Teilaufgaben inline ausklappen (wenn vorhanden)
- Tap auf eine Aufgabe ohne Teilaufgaben → Teilaufgaben-Fenster öffnen (dort können neue angelegt werden)
- Tap auf eine Teilaufgabe → Teilaufgaben-Fenster öffnen
- Status-Icon rechts (Kreis/Haken/Pfeil) → Menu mit Status-Optionen

**Fortschrittsring:** Oben rechts neben dem Datum zeigt ein grüner Ring den Tagesfortschritt (erledigte/gesamte Aufgaben).

**Musik-Leiste:** Bei aktiver Musikwiedergabe erscheint eine kompakte Player-Leiste mit Track-Info, Play/Pause und AirPlay/Bluetooth-Picker.

### 2.2 Aufgaben (Editor)

Zeigt alle aktiven Aufgaben, gruppiert nach Kategorien. Pro Wiederholungsgruppe wird nur eine Instanz angezeigt (die nächste offene oder die neueste).

**Bedienung:**
- Tap auf Aufgabe → EditTaskView (Bearbeitungsformular)
- Tap auf Teilaufgaben-Badge → Teilaufgaben inline ausklappen
- Swipe links → Aufgabe löschen (löscht alle Wiederholungen)
- Drag & Drop → Sortierreihenfolge ändern

**Toolbar:**
- Links: Kategorie-Manager (Schrank-Icon) + Template-Manager (Dokument-Icon)
- Rechts: Plus → Dialog „Neue Aufgabe" oder „Aus Template"

### 2.3 Historie

Read-only-Ansicht aller erledigten und übersprungenen Aufgaben, hierarchisch geordnet:

**Ebenen:** Kalenderwoche → Tag → Kategorie → Aufgaben

Jede Ebene ist einzeln auf-/zuklappbar. Initial ist die neueste Woche geöffnet, darin der neueste Tag mit allen Kategorien.

**Bedienung:**
- Tap auf Aufgabe → Teilaufgaben inline ausklappen (ohne Status-Buttons, nur Anzeige)
- Keine Bearbeitungsmöglichkeit — die Historie ist ein Protokoll

### 2.4 Einstellungen

Konfiguration der App, aufgeteilt in Bereiche:

| Bereich | Funktionen |
|---|---|
| **Sprache & Darstellung** | Sprache (via iOS Settings), Erscheinungsbild (System/Hell/Dunkel) |
| **Aufgaben-Verhalten** | Standard-Gültigkeitsdauer, Standard-Wiederholungsintervall, Verfallfrist |
| **Benachrichtigungen** | Erinnerungen, Tägliche Zusammenfassung (noch nicht aktiv) |
| **Klang & Haptik** | Töne an/aus, Lautstärke, Klangwelt für Tag-Beginn/Ende, Haptisches Feedback |
| **Musik** | Apple Music (MusicKit) — Wiedergabe und Mediathek-Zugriff. Setzt ein aktives Apple-Music-Abo voraus. |
| **Daten** | Export (JSON), Import, Duplikat-Bereinigung, Alle Daten zurücksetzen |
| **Demo & Präsentation** | Wechsel in eine isolierte Demo-Umgebung mit Beispieldaten zum Ausprobieren — siehe §11 |
| **Über MyCadenza** | Version, „Was ist neu" (manueller Aufruf der Versionshinweise), Datenschutzrichtlinie, Support & Feedback |

---

## 3. Aufgaben-System

### 3.1 Hauptaufgaben

Jede Aufgabe hat:

| Eigenschaft | Beschreibung |
|---|---|
| **Titel** | Frei wählbarer Name |
| **Startzeit** | Wann die Aufgabe fällig wird (Default: nächste volle halbe Stunde) |
| **Startdatum** | An welchem Tag (bei Erstellung automatisch berechnet: Uhrzeit in der Zukunft → heute, sonst morgen) |
| **Gültigkeitsdauer** | Wie lange die Aufgabe offen bleibt (0,5 bis 24 Stunden) |
| **Wiederholung** | Einmalig, Täglich, Arbeitstäglich, Wöchentlich, Monatlich, Individuell |
| **Kategorie** | Optional, bestimmt Farbe und Klangwelt |
| **Farbe** | Aus 9×5 kuratierter Palette (wird von Kategorie übernommen) |
| **Priorität** | Normal oder Hoch (rotes Dreieck-Icon) |
| **Erinnerung** | Lokale Notification zur Startzeit |
| **Kein Verfall** | Nur bei einmaligen Aufgaben: bleibt offen bis manuell erledigt |

### 3.2 Status-Logik

Drei Status: **Offen**, **Erledigt**, **Übersprungen**

**Automatische Ableitung:** Wenn eine Aufgabe Teilaufgaben hat, wird der Status automatisch berechnet:
- Alle Teilaufgaben erledigt → Hauptaufgabe erledigt
- Alle Teilaufgaben erledigt oder übersprungen → Hauptaufgabe übersprungen
- Sonst → Hauptaufgabe offen

**Manuelle Überschreibung:** Setzt der Nutzer den Status der Hauptaufgabe selbst (über das Status-Menu rechts), bleibt diese Wahl bestehen, auch wenn sich Teilaufgaben anschließend ändern — die manuelle Wahl **friert** die automatische Ableitung ein. Erst wenn die Hauptaufgabe wieder auf „offen" gesetzt wird, übernimmt die App die automatische Ableitung. Dabei werden alle offenen Teilaufgaben jeweils auf den gleichen Status gesetzt.

**Verfallfrist und automatisches Überspringen:** Wenn die Gültigkeitsdauer abläuft, wird die Aufgabe nicht sofort übersprungen — sie bleibt für eine konfigurierbare Verfallfrist (Default: 1 Stunde) „überfällig aber offen". Innerhalb dieser Frist kann der Nutzer die Aufgabe noch erledigen. Erst danach wird sie automatisch als übersprungen markiert; alle offenen Teilaufgaben werden ebenfalls übersprungen.

### 3.3 Wiederholungen

Bei wiederkehrenden Aufgaben erstellt die App automatisch die nächste Instanz, sobald die aktuelle Instanz erledigt, übersprungen oder abgelaufen ist. Die neue Instanz:

- Behält alle Eigenschaften (Titel, Farbe, Kategorie, Teilaufgaben, Priorität, Erinnerung)
- Hat die berechnete nächste Startzeit
- Hat alle Teilaufgaben im Status „offen"
- Teilt die gleiche `repeatGroupID` (für Gruppierung im Editor)

**Individuelles Intervall:** z. B. „Alle 3 Arbeitstage" oder „Alle 2 Wochen".

### 3.4 Teilaufgaben

Jede Hauptaufgabe kann beliebig viele Teilaufgaben haben. Teilaufgaben haben nur einen Titel und einen Status.

**Sortierung:** Per Drag & Drop in der Liste umsortierbar.

**Hinzufügen:** Über Plus-Button oder inline-Textfelder (Return-Taste springt zur nächsten Zeile).

### 3.5 Aufgaben ohne Verfall (doesNotExpire)

Spezialfall für einmalige Aufgaben wie Einkaufslisten oder Projektpläne:
- Läuft nicht ab, bleibt offen bis manuell erledigt
- Erscheint im „Ausblick", wenn das Startdatum in der Zukunft liegt
- Hat zusätzliche Aufräum-Optionen:
  - **Erledigte entfernen:** Erstellt einen Historie-Snapshot, löscht dann die erledigten/übersprungenen Teilaufgaben
  - **Alle zurücksetzen:** Erstellt einen Historie-Snapshot, setzt alle Teilaufgaben auf offen zurück

Beide Operationen werden zentral verwaltet und geben aus jeder Aufrufstelle das gleiche akustische Feedback (Cleanup- und Reset-Sound).

---

## 4. Kategorien

Kategorien organisieren Aufgaben visuell und akustisch.

| Eigenschaft | Beschreibung |
|---|---|
| **Name** | z. B. „Gesundheit", „Haushalt", „Arbeit" |
| **Farbe** | Aus der 9×5 Palette, wird an Aufgaben und Templates vererbt |
| **Icon** | SF Symbol aus 6 Gruppen (Allgemein, Alltag, Arbeit, Gesundheit, Freizeit, Sonstiges) |
| **Klangwelt** | Bestimmt welche Sounds bei Aufgaben dieser Kategorie gespielt werden |
| **Sortierung** | Per Drag & Drop im Kategorie-Manager |

**Farbvererbung:** Wenn die Kategorie einer Aufgabe geändert wird, übernimmt die Aufgabe automatisch die Farbe der neuen Kategorie. Wird die Farbe der Kategorie geändert, werden alle zugehörigen Aufgaben und Templates aktualisiert.

**Löschen:** Beim Löschen einer Kategorie bleiben die zugehörigen Aufgaben und Templates erhalten, verlieren aber ihre Kategorie-Zuordnung.

---

## 5. Templates

Templates sind Vorlagen für Aufgaben, die häufig mit den gleichen Einstellungen erstellt werden.

**Erstellen:** Im Template-Manager (über Toolbar in der Aufgabenliste) oder direkt aus einer bestehenden Aufgabe („Als Template speichern" in der EditTaskView).

**Verwenden:** Beim Erstellen einer neuen Aufgabe „Aus Template" wählen → TemplatePickerView → CreateTaskFromTemplateView. Alle Werte sind vorausgefüllt, aber einzeln anpassbar.

**Inhalt:** Gleiche Felder wie eine Aufgabe (Titel, Farbe, Startzeit, Gültigkeit, Wiederholung, Kategorie, Priorität, Erinnerung, Teilaufgaben), aber ohne Status und ohne aktive Instanzen.

---

## 6. Klang-System

### 6.1 Konzept

Klänge und haptisches Feedback begleiten alle wichtigen Übergänge — Tagesablauf, Aufgabenstatus, Datenoperationen. Die Aktionen sind in vier Gruppen organisiert:

| Gruppe | Beispiele |
|---|---|
| **Tagbezug** | Tag begonnen (App-Start), Tag komplett (alle Aufgaben erledigt), Tag beendet |
| **Aufgabenbezug** | Aufgabe fällig, überfällig, verfallen; Aufgabe erstellt, gelöscht; Erledigte entfernt, Aufgabe zurückgesetzt |
| **Teil- und Hauptaufgabe** | Teilaufgabe erledigt/übersprungen; Hauptaufgabe erledigt/übersprungen/komplett; Überfällige Aufgabe doch noch erledigt |
| **Settings** | Export oder Import abgeschlossen |

Jede Aktion hat einen eigenen Klang, der pro Klangwelt unterschiedlich klingt. Aufgaben mit hoher Priorität werden etwas lauter gespielt.

### 6.2 Klangwelten

Fünf verschiedene Sound-Sets mit unterschiedlichem Charakter:

| Klangwelt | Charakter |
|---|---|
| **System** ⚙️ | Klassische iOS-System-Sounds (kein eigenes Sound-Set) |
| **Morgenwald** 🌿 | Natur, Vögel, Wasser, Wind |
| **Salon** 🎹 | Klavier, Akustik, Kammermusik |
| **Cityflow** 🏙️ | Urban, Lo-Fi, Café-Atmosphäre |
| **Horizont** 🌌 | Ambient, elektronische Pads |

**Zuordnung:** Jede Kategorie hat eine Klangwelt. Tag-Beginn/Ende hat eine eigene globale Einstellung.

> **Hinweis:** Klangwelten und Sound-Aktionen werden nach v1.7.2 in einem vollständigen Redesign überarbeitet. Der hier dokumentierte Stand bildet die technische Basis ab, kann sich konzeptionell aber noch substantiell ändern.

### 6.3 Haptik

Jede Aktion hat ein eigenes haptisches Muster:
- Teilaufgabe erledigt: Leichter Impact (medium bei hoher Priorität)
- Teilaufgabe übersprungen: Sanfter Impact
- Hauptaufgabe komplett: Success-Notification
- Tag-Beginn/Ende: Medium Impact

### 6.4 Audio-Verhalten

- Audio Session: `.ambient` — respektiert den Silent-Switch und mischt mit anderer Musik
- Sounds liegen als WAV (48 kHz) im Bundle, gegliedert nach Klangwelt
- Dateinamen: `{klangwelt}_{aktion}.wav` (z. B. `morgenwald_teilaufgabeErledigt.wav`)

---

## 7. Farbsystem

### 7.1 Die Palette

9 Farbfamilien mit je 5 Intensitätsstufen:

| Familie | Verwendungshinweis |
|---|---|
| Blau | Beruflich / Kommunikation |
| Petrol | Gesundheit / Natur |
| Bernstein | Organisation / Finanzen |
| Korall | Sozial / Warmherzig |
| Violett | Kreativ / Persönlich |
| Pink | Beziehungen / Familie |
| Grün | Haushalt / Erledigung |
| Rot | Dringend / Wichtig |
| Grau | Neutral / Archiv |

### 7.2 Light/Dark Mode

Jeder Farbstop hat einen Light- und einen Dark-Mode-Hex-Wert. Gespeichert wird immer der Light-Mode-Wert. Die App löst den passenden Wert je nach aktuellem Farbschema auf.

### 7.3 Gold-Chrome-Akzent

Für UI-Elemente der App selbst (nicht für Aufgaben):
- **Gold Standard:** Toggles, Badges, Tab-Tint
- **Gold Dunkel:** Überfällig, Übersprungen
- **Gold Hell:** Highlights
- **Gold Original (#C8973A):** Splash Screen, App Icon

---

## 8. Daten & Synchronisation

### 8.1 iCloud-Sync

Alle Daten werden automatisch über iCloud synchronisiert. Änderungen auf einem Gerät erscheinen auf allen Geräten mit derselben Apple ID.

**Duplikat-Bereinigung:** Nach jedem Sync prüft die App auf Duplikate (gleiche Aufgabe am gleichen Tag) und entfernt diese automatisch.

### 8.2 Export/Import

**Export:** Speichert alle Kategorien, Aufgaben und Templates als JSON-Datei. Kann per Share Sheet geteilt werden.

**Import:** Liest eine JSON-Export-Datei ein. Zwei Modi:
- **Ersetzen:** Löscht alle bestehenden Daten und ersetzt sie
- **Zusammenführen:** Fügt importierte Daten hinzu, bestehende Kategorien werden aktualisiert

### 8.3 Hintergrund-Verarbeitung

Die App verarbeitet Aufgaben auch im Hintergrund:
- Abgelaufene Aufgaben durchlaufen die Verfallfrist und werden danach automatisch übersprungen
- Nächste Wiederholungen werden erstellt
- Badge-Zähler wird aktualisiert
- Background Refresh alle 15 Minuten — crash-fest seit Build 10715

---

## 9. Apple-Music-Integration

MyCadenza bindet Apple Music über MusicKit an. Aktive Wiedergabe erscheint als kompakte Player-Leiste in der Heute-Ansicht — mit Titel, Interpret, Artwork, Play/Pause und AirPlay/Bluetooth-Picker.

**Voraussetzung:** Apple-Music-Abo. Beim ersten Aufruf fragt die App die Berechtigung zum Zugriff auf die Mediathek ab.

**Hinweis:** MusicKit funktioniert nicht im iOS Simulator — Tests und Demonstrationen brauchen ein echtes Gerät.

> **Hinweis:** Funktionsumfang und UI werden im Klangwelt-Redesign nach v1.7.2 erweitert/überarbeitet.

---

## 10. „Was ist neu" und Update-Hinweis

**„Was ist neu":** Nach einem Versionsupdate zeigt MyCadenza beim ersten Start automatisch ein Sheet mit den wichtigsten Änderungen der neuen Version. Neue Nutzer (gerade erst durch das Onboarding gegangen) sehen es nicht. Zusätzlich kann das Sheet jederzeit manuell aus den Einstellungen aufgerufen werden — Bereich „Über MyCadenza" → „Was ist neu".

**Update-Hinweis:** Beim App-Start und bei jedem Wechsel in den Vordergrund prüft die App (max. einmal pro Tag), ob im App Store eine neuere Version verfügbar ist. Falls ja, erscheint ein dezenter Alert mit „Aktualisieren"-Button.

---

## 11. Demo & Präsentation

MyCadenza enthält eine eigene Demo-Umgebung, die separat von den Echtdaten läuft. Sie ist als reguläres Endnutzer-Feature in den Einstellungen erreichbar — Bereich „Demo & Präsentation".

**Wozu:** Zum Ausprobieren der App-Funktionen, für Vorführungen und für Screenshots — ohne die eigenen Aufgaben anzufassen.

**Aktivierung und Wechsel:**
- Aktivieren über die Sektion „Demo & Präsentation" in den Einstellungen. Bestätigung erforderlich, App-Neustart erforderlich.
- Echtdaten bleiben unberührt — Produktion und Präsentation verwenden physisch getrennte Datenspeicher.
- **Reset on Activation:** Bei jeder Aktivierung werden frische Demo-Daten geladen — Änderungen aus früheren Demo-Sitzungen gehen verloren. Der Hilfetext im Aktivierungs-Dialog weist darauf hin.
- Zurück zu den Echtdaten: Sektion „Demo & Präsentation" → „Präsentationsmodus beenden", dann App-Neustart.

---

## 12. Geplante Features

| Feature | Beschreibung |
|---|---|
| Klangwelten-Redesign (nach v1.7.2) | Vollständige konzeptionelle Überarbeitung der Klangwelten und Sound-Aktionen |
| In-App Sprachauswahl | Sprache direkt in der App wählen statt über iOS Settings |
| Tägliche Zusammenfassung | Push-Notification mit Tagesübersicht zur konfigurierbaren Uhrzeit |
| Algorithmische Tagesmelodie | Generierte Kurzmelodie als musikalisches Abbild des Tagesfortschritts |
| Photo-Attachments auf Aufgaben | Bilder an Haupt-/Teilaufgaben und Templates anhängen, mit Ablaufsystem |
| CarPlay-Integration | Musiksteuerung aus dem Auto |
| EventKit-Integration | Apple Reminders + Calendar-Anbindung für Wiedervorlagen und Fristen |
| Apple Contacts Integration | Kontakte-Verknüpfung für aufgabenbezogene Personen |
| Spotify-Integration | Alternative Musikquelle (Service-Protokoll bereits vorbereitet) |
| Quick Start (Anwender-Doku) | Kurzeinführung auf der Website |
| Vollständige Doku (Anwender-Doku) | Detailreferenz auf der Website |
