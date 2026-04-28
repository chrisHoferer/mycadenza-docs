# MyCadenza – Funktionsübersicht

> Dokumentation der App-Funktionsweise für den Entwickler.
> Stand: 30.03.2026 · v1.5.2 (Build 10506)

---

## 1. Was MyCadenza macht

MyCadenza ist ein persönlicher Tagesplaner für iPhone und iPad. Die App organisiert wiederkehrende Aufgaben mit Teilaufgaben, Kategorien und akustischem Feedback. Der Fokus liegt auf dem aktuellen Tag: Was ist fällig, was kommt noch, was ist erledigt?

**Kernidee:** Jede Aufgabe hat eine Startzeit und eine Gültigkeitsdauer. Ist die Zeit abgelaufen, wird die Aufgabe automatisch als übersprungen markiert und die nächste Wiederholung erstellt. Der Nutzer muss sich nicht um Planung kümmern — die App erledigt das.

---

## 2. Die vier Hauptbereiche (Tabs)

### 2.1 Heute

Die zentrale Ansicht. Zeigt alle Aufgaben des aktuellen Tages, aufgeteilt in fünf Sektionen:

| Sektion | Inhalt | Initial |
|---|---|---|
| **Überfällig** | Aufgaben, deren Gültigkeitsdauer abgelaufen ist, die aber noch offen sind | Offen |
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

### 2.2 Aufgaben (Editor)

Zeigt alle aktiven Aufgaben, gruppiert nach Kategorien. Pro Wiederholungsgruppe wird nur eine Instanz angezeigt (die nächste offene oder die neueste).

**Bedienung:**
- Tap auf Aufgabe → EditTaskView (Bearbeitungsformular)
- Tap auf Teilaufgaben-Badge → Teilaufgaben inline ausklappen
- Swipe links → Aufgabe löschen (löscht alle Wiederholungen)
- Drag & Drop → Sortierreihenfolge ändern

**Toolbar:**
- Links: Kategorie-Manager (Schrank-Icon) + Template-Manager (Dokument-Icon)
- Rechts: Plus → Dialog "Neue Aufgabe" oder "Aus Template"

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
| **Aufgaben-Verhalten** | Standard-Gültigkeitsdauer, Standard-Wiederholungsintervall |
| **Benachrichtigungen** | Erinnerungen, Tägliche Zusammenfassung (beides noch nicht aktiv) |
| **Klang & Haptik** | Töne an/aus, Lautstärke, Klangwelt für Tag-Beginn/Ende, Haptisches Feedback |
| **Musik** | Musikanbindung (noch nicht aktiv) |
| **Daten** | Export (JSON), Import, Duplikat-Bereinigung, Alle Daten zurücksetzen |
| **Über MyCadenza** | Version, Datenschutzrichtlinie, Support & Feedback |

---

## 3. Aufgaben-System

### 3.1 Hauptaufgaben

Jede Aufgabe hat:

| Eigenschaft | Beschreibung |
|---|---|
| **Titel** | Frei wählbarer Name |
| **Startzeit** | Wann die Aufgabe fällig wird |
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

**Manuelle Überschreibung:** Der Nutzer kann den Status der Hauptaufgabe jederzeit manuell setzen (über das Status-Menu rechts). Dabei werden alle offenen Teilaufgaben auf den gleichen Status gesetzt.

**Automatisches Überspringen:** Wenn die Gültigkeitsdauer abläuft und die Aufgabe noch offen ist, wird sie automatisch als übersprungen markiert. Alle offenen Teilaufgaben werden ebenfalls übersprungen.

### 3.3 Wiederholungen

Bei wiederkehrenden Aufgaben erstellt die App automatisch die nächste Instanz, sobald die aktuelle Instanz erledigt, übersprungen oder abgelaufen ist. Die neue Instanz:

- Behält alle Eigenschaften (Titel, Farbe, Kategorie, Teilaufgaben, Priorität, Erinnerung)
- Hat die berechnete nächste Startzeit
- Hat alle Teilaufgaben im Status "offen"
- Teilt die gleiche `repeatGroupID` (für Gruppierung im Editor)

**Individuelles Intervall:** z.B. "Alle 3 Arbeitstage" oder "Alle 2 Wochen".

### 3.4 Teilaufgaben

Jede Hauptaufgabe kann beliebig viele Teilaufgaben haben. Teilaufgaben haben nur einen Titel und einen Status.

**Sortierung:** Per Drag & Drop in der Liste umsortierbar.

**Hinzufügen:** Über Plus-Button oder inline-Textfelder (Return-Taste springt zur nächsten Zeile).

### 3.5 Aufgaben ohne Verfall (doesNotExpire)

Spezialfall für einmalige Aufgaben wie Einkaufslisten oder Projektpläne:
- Läuft nicht ab, bleibt offen bis manuell erledigt
- Erscheint im "Ausblick" wenn das Startdatum in der Zukunft liegt
- Hat zusätzliche Aufräum-Optionen:
  - **Erledigte entfernen:** Erstellt einen Historie-Snapshot, löscht dann die erledigten/übersprungenen Teilaufgaben
  - **Alle zurücksetzen:** Erstellt einen Historie-Snapshot, setzt alle Teilaufgaben auf offen zurück

---

## 4. Kategorien

Kategorien organisieren Aufgaben visuell und akustisch.

| Eigenschaft | Beschreibung |
|---|---|
| **Name** | z.B. "Gesundheit", "Haushalt", "Arbeit" |
| **Farbe** | Aus der 9×5 Palette, wird an Aufgaben und Templates vererbt |
| **Icon** | SF Symbol aus 6 Gruppen (Allgemein, Alltag, Arbeit, Gesundheit, Freizeit, Sonstiges) |
| **Klangwelt** | Bestimmt welche Sounds bei Aufgaben dieser Kategorie gespielt werden |
| **Sortierung** | Per Drag & Drop im Kategorie-Manager |

**Farbvererbung:** Wenn die Kategorie einer Aufgabe geändert wird, übernimmt die Aufgabe automatisch die Farbe der neuen Kategorie. Wird die Farbe der Kategorie geändert, werden alle zugehörigen Aufgaben und Templates aktualisiert.

**Löschen:** Beim Löschen einer Kategorie bleiben die zugehörigen Aufgaben und Templates erhalten, verlieren aber ihre Kategorie-Zuordnung.

---

## 5. Templates

Templates sind Vorlagen für Aufgaben, die häufig mit den gleichen Einstellungen erstellt werden.

**Erstellen:** Im Template-Manager (über Toolbar in der Aufgabenliste) oder direkt aus einer bestehenden Aufgabe ("Als Template speichern" in der EditTaskView).

**Verwenden:** Beim Erstellen einer neuen Aufgabe "Aus Template" wählen → TemplatePickerView → CreateTaskFromTemplateView. Alle Werte sind vorausgefüllt, aber einzeln anpassbar.

**Inhalt:** Gleiche Felder wie eine Aufgabe (Titel, Farbe, Startzeit, Gültigkeit, Wiederholung, Kategorie, Priorität, Erinnerung, Teilaufgaben), aber ohne Status und ohne aktive Instanzen.

---

## 6. Klang-System

### 6.1 Konzept

Fünf Aktionen lösen Klänge und haptisches Feedback aus:

| Aktion | Auslöser | Sound-Dauer |
|---|---|---|
| Tag begonnen | App-Start (zukünftig) | 2 Sek. |
| Teilaufgabe erledigt | Teilaufgabe auf "erledigt" gesetzt | 0,5 Sek. |
| Teilaufgabe übersprungen | Teilaufgabe auf "übersprungen" gesetzt | 0,5 Sek. |
| Hauptaufgabe komplett | Alle Teilaufgaben durch oder manuell erledigt | 1,5 Sek. |
| Tag beendet | App-Ende (zukünftig) | 2,5 Sek. |

### 6.2 Klangwelten

Vier verschiedene Sound-Sets mit unterschiedlichem Charakter:

| Klangwelt | Charakter | Status |
|---|---|---|
| **Morgenwald** 🌿 | Natur, Vögel, Wasser, Wind | ✅ Implementiert |
| **Salon** 🎹 | Klavier, Akustik, Kammermusik | ✅ Implementiert |
| **Cityflow** 🏙️ | Urban, Lo-Fi, Café-Atmosphäre | ⬜ Noch zu generieren |
| **Horizont** 🌌 | Ambient, elektronische Pads | ⬜ Noch zu generieren |

**Zuordnung:** Jede Kategorie hat eine Klangwelt. Tag-Beginn/Ende hat eine eigene globale Einstellung.

**Priorität:** Aufgaben mit hoher Priorität werden etwas lauter gespielt (Faktor 1.0 vs. 0.85).

### 6.3 Haptik

Jede Aktion hat ein eigenes haptisches Muster:
- Teilaufgabe erledigt: Leichter Impact (medium bei hoher Priorität)
- Teilaufgabe übersprungen: Sanfter Impact
- Hauptaufgabe komplett: Success-Notification
- Tag-Beginn/Ende: Medium Impact

### 6.4 Audio-Verhalten

- Audio Session: `.ambient` — respektiert den Silent-Switch und mischt mit anderer Musik
- Sounds liegen als WAV (48kHz) im Bundle-Root
- Dateinamen: `{klangwelt}_{aktion}.wav` (z.B. `morgenwald_teilaufgabeErledigt.wav`)

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
- Abgelaufene Aufgaben werden automatisch übersprungen
- Nächste Wiederholungen werden erstellt
- Badge-Zähler wird aktualisiert
- Background Refresh alle 15 Minuten

---

## 9. Präsentationsmodus (nur Debug)

Für App Store Screenshots: Sichert die Echtdaten lokal, lädt englische Demo-Daten mit 6 Kategorien und realistischen Aufgaben über mehrere Tage. Wiederherstellung nur auf dem gleichen Gerät möglich.

---

## 10. Geplante Features

| Feature | Beschreibung |
|---|---|
| Cityflow + Horizont | Zwei weitere Klangwelten via ElevenLabs generieren |
| Standard-Werte in AddTaskView | defaultValidityDuration und defaultRepeatInterval aus Settings übernehmen |
| In-App Sprachauswahl | Sprache direkt in der App wählen statt über iOS Settings |
| Datenschutzrichtlinie | URL in den Einstellungen hinterlegen |
| Support & Feedback | Kontaktmöglichkeit in den Einstellungen |
| Musik-Integration | Apple Music / Spotify Anbindung (Playlist pro Kategorie) |
| Tägliche Zusammenfassung | Push-Notification mit Tagesübersicht zur konfigurierbaren Uhrzeit |
| Algorithmische Tagesmelodie | Generierte Kurzmelodie als musikalisches Abbild des Tagesfortschritts |
