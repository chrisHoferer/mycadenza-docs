# MyCadenza – ToDo-Liste

> Konsolidierte Roadmap nach Code Review v1.7.0
> Stand: 29.04.2026 · Build 10717 gepusht und getaggt · F12 entdeckt

---

## Struktur

Die Aufgaben sind in vier Blöcke aufgeteilt:

| Block | Inhalt | Leitgedanke |
|---|---|---|
| **A) v1.7.0** | Bugfix & Korrektheit | Was kaputt ist, reparieren + Semantik-Korrektheit |
| **B) v1.7.1** | Code-Qualität, Design, Plan-vs-Realität | Was strukturell besser werden soll |
| **C) Feature Pool Zukunft** | Noch nicht geplante Features | Ideen & Backlog |
| **D) Website** | Anwender-Dokumentation | Schicht 3 + 4 |

> **Hinweis zur Release-Strategie:** Builds 10713–10724 wurden ursprünglich teilweise als v1.7.1 geplant. Da v1.7.0 noch nicht released ist, laufen sie alle in den v1.7.0-Train. Die formale Trennung v1.7.0/v1.7.1 startet erst nach App-Store-Release.

---

## A) v1.7.0 – Bugfix & Korrektheit

### Bugs (akut)

- [x] **Bug:** Löschfunktion in der Teilaufgabenliste (Icon) funktioniert nicht — Build 10709
- [x] **Bug:** TestFlight-Installation führt konstant zu App-Abstürzen — gelöst in Build 10715 (TaskScheduler `@MainActor`-Refactor). Vorab passiv 24h verifiziert, Crash-Stack zeigte Background-Task → SwiftData Thread-Violation, Ursache: B-M1-Fix (10705) hatte das Race-Condition-Problem freigelegt.
- [x] **Bug:** Bearbeiten von Teilaufgaben kann nicht abgeschlossen werden — Build 10709

### Code-Review-Etappen

- [x] **Build 10707** — TestFlight-Hotfix-Welle (interne Korrekturen)
- [x] **Build 10708** — TestFlight-Hotfix-Welle (interne Korrekturen)
- [x] **Build 10709** — Bugfixes (Trash-Icon, SubTaskEditor-Linebreaks)
- [x] **Build 10710** — C-M1 (WhatsNewView) + C-M2 (HistoryView-Datumsformat)
- [x] **Build 10711** — A-S1 komplett (Model-API `markDone/markSkipped/markOpen`, Setter-Side-Effect entfernt, 8/9 Call-Sites migriert, A-A1 gelöst)
- [x] **Build 10712a** — B2 Default-Startzeit (nächste halbe Stunde mit Minuten-Granularität, Speicher-Fallback bei Vergangenheit, `Date+MyCadenza.swift` neu angelegt)
- [x] **Build 10712b** — B3 Vorab-erledigte Zukunftsaufgaben in Abgeschlossen, `syncStatus()` pflegt `lastExecutedDate` konsistent (idempotent), Fortschrittsring gegen Überlauf geschützt
- [x] **Build 10712c** — F2 `markOpen` reicht Status auf Teilaufgaben durch, `markOpen` entsperrt grundsätzlich (`isManualStatus = false`)
- [x] **CloudKit-Schema-Check (Production)** — am 25.04.2026 durchgeführt. Production- vs. Development-Schema sind deckungsgleich (alle 6 Record-Types, alle Feldnamen identisch). Damit Schema-Mismatch als Crash-Ursache ausgeschlossen.
- [x] **Build 10715** — TaskScheduler `@MainActor`-Refactor (B-S1) + TestFlight-BG-Task-Crash-Fix. Variante B aus der Refactor-Diskussion: `@MainActor` auf gesamte Klasse, BG-Closure hopt mit `Task { @MainActor in ... }`, `DispatchWorkItem` → `Task` migriert. Build grün ohne CadenzaApp-Anpassung. Drei-Stufen-Test (Simulator → echtes iPhone → TestFlight) erfolgreich. TestFlight-Verifikation läuft passiv 24h.
- [x] **Build 10716** — SubTask-Operations konsolidieren (C-S37). Neue Datei `MainTask+SubTaskOperations.swift` mit `@MainActor`-Extension, drei vorher in `EditTaskView`/`TodayTaskRowView`/`SubTaskSheetView` duplizierte Methoden zusammengeführt. Drei Sub-Bugfixes als Beifang: (1) `isManualStatus = false` vor `syncStatus()` jetzt überall konsistent, (2) `playCompletedRemoved`-Sound im SubTaskSheetView jetzt vorhanden (vorher fehlte er komplett), (3) `playTaskReset`-Sound im SubTaskSheetView jetzt vorhanden. Cleanup-Unlock-Ausreißer aus 10711 mitgelöst.
- [x] **Build 10717** — C-S38 + C-S39 + F10. `EditTaskView` nutzt jetzt `SubTaskRowView` statt eigener HStack-Toggle-Implementierung (DRY mit `SubTaskSheetView`, -34 Zeilen). Status-Action-Logik aus `TodayTaskRowView` Menu-Buttons in `MainTask+Actions.swift` verschoben — drei neue `@MainActor`-Methoden `performMarkOpen()`, `performMarkDone(context:)`, `performMarkSkipped(context:)`. Datei `MainTask+SubTaskOperations.swift` -> `MainTask+Actions.swift` umbenannt, beide Extensions koexistieren mit MARK-Trennung. F10 als Beifang gelöst — Sounds (`playSubTaskDone/Skipped/TaskReset`) in `EditTaskView` jetzt vorhanden.
- [ ] **Build 10718** — F11-Diagnose: `playCompletedRemoved`/`playTaskReset` möglicherweise nicht hörbar in EditTaskView. Voraussetzung: Begriffs-Glossar für die View-Hierarchie (siehe unten). Diagnose mit präzisem Schritt-für-Schritt-Verfahren.
- [ ] **Build 10719** — Tote-Code-Etappe (C-S5, C-S16, C-S18, C-S22, C-S29; C-M3 nebenbei). Guter Zeitpunkt auch für F1, F8 als Beifang, falls umfangsverträglich.
- [ ] **Build 10720** — Localization-Cleanup (dynamische `String(localized:)`-Keys auf `String(format:)` umstellen; hardcoded deutsche Strings in Pickern)
- [ ] **Build 10721** — Import-Sperre bei aktivem CloudKit-Sync (B-M2) — letzter v1.7.0-Release-Blocker
- [ ] **Build 10722** — Sound-Kaskaden (B-S2, abhängig von Klangwelten-Entscheidung)
- [ ] **Build 10723** — MusicPlayerView Konsolidierung (C-S43, C-S44, C-S45, C-S46)
- [ ] **Build 10724** — SoundManager preview-API (C-S42)

### Offene Code-Quality-Items

- [x] `@Relationship inverse:` Attribute in `Models.swift` und `TaskTemplate.swift` ergänzt
- [ ] `cleanupDuplicates()` – jetzt preservation-score-basiert, bleibt flagged (Review)
- [ ] `processAllTasks()` – Debounce- und ScenePhase-Handler-Cleanup finalisieren
- [ ] xcstrings – 1–2 verbleibende Artefakte bereinigen

### Erledigte Verifikations-Punkte

- [x] **Test 2 zu 10712a (Minuten-Granularität)** — am 25.04.2026 nebenbei beim 10716-Test erledigt. Eine Test-Aufgabe wurde im Zeitfenster um die halbe Stunde herum angelegt; die Startzeit ist nicht auf die nächste halbe Stunde gesprungen, sondern wurde korrekt auf die aktuelle Minute gesetzt. Damit Minuten-Granularität verifiziert.

---

## B) v1.7.1 – Code-Qualität, Design & Plan-vs-Realität

### Plan-vs-Realität-Block

Zusammengehörige UX-Findungen mit gemeinsamem Nenner: Die App denkt zu stark in `startTime` (Plan), nicht in `lastExecutedDate` (Realität).

- [ ] **B1 · Historie als echtes Protokoll**
  Heute: Erledigte Aufgaben werden nur angezeigt, solange sie nicht gelöscht sind — die Historie ist ein Anzeigefilter auf noch-existierende Objekte, kein echtes Protokoll. Wenn eine Wiederholungsgruppe in der EditorView gelöscht wird, verschwindet die Vergangenheit mit. Ziel: Beim Abschluss einer Aufgabe (done/skipped) einen unveränderlichen Snapshot in der Historie ablegen, analog zum bestehenden `TaskSnapshotHelper`. Löschen der aktiven Aufgabe berührt die Historie-Snapshots nicht.
  **Offene Fragen:** Eigenes Model oder `isActive = false`-Marker? Anzeige-Semantik in HistoryView (Gruppierung nach `lastExecutedDate` statt `startTime`)? Retention-Policy? Fallback-Verhalten für Altdaten ohne `lastExecutedDate`?

- [x] **B2 · Default-Startzeit beim Anlegen** — erledigt in Build 10712a. Nächste halbe Stunde ab jetzt (Minuten-Granularität), Datumswechsel bei Mitternachts-Überschreitung, Speicher-Fallback auf `Date()` wenn gewählte Zeit heute in der Vergangenheit liegt.

- [x] **B3 · Abgeschlossen inklusive Vorarbeit** — erledigt in Build 10712b. Entscheidung aus der Konzeptsession: Teilweise bearbeitete Zukunftsaufgaben bleiben in der Ausblick-Sektion (Plan führt, Fortschritt begleitet); keine eigene "Vorgearbeitet"-Sektion. Vollständig heute abgeschlossene Zukunftsaufgaben erscheinen in Abgeschlossen, zählen aber nicht in den Tagespensum-Fortschrittsring.

### UX-Findings für v1.7.1

- [ ] **F1 · Abgeschlossen-Sektion Darstellung schärfen**
  Der linke Kategorie-Farbbalken und das Reorder-Handle (die drei Striche rechts) wirken in der Abgeschlossen-Sektion unpassend — erledigte Aufgaben sind "Ergebnisbereich", nicht "Aktionsbereich". Ziele:
  - **Farbbalken nach Status:** grün für `done`, gold-gedämpft für `skipped` — statt Kategorie-Farbe.
  - **Status-Haken-Farbe konsistent:** Aktuell ist der Hauptaufgaben-Status-Haken in Abgeschlossen gold, sollte aber zur Status-Logik passen — grün für `done`, gold für `skipped`. Konsistent mit dem Farbbalken.
  - **Reorder-Handle entfernen:** Sortieren in Abgeschlossen ergibt inhaltlich keinen Sinn.
  - **Keine eigene View:** Änderungen konditional in `TodayTaskRowView` anhand des Sektionskontexts. Bewusst *nicht* als eigene Zeilenvariante implementiert — würde die Komplexität unverhältnismäßig erhöhen.
  Kandidat für Build 10719 (Tote-Code/Cleanup) als Beifang.

- [x] **F2 · `markOpen` soll Teilaufgaben durchreichen** — erledigt in Build 10712c. `.open`-Case in `applyStatusToSubTasks()` setzt jetzt alle `done`/`skipped`-Teilaufgaben auf `open` zurück. `markOpen` entsperrt grundsätzlich (`isManualStatus = false`), symmetrisch zu `markDone`/`markSkipped`.

- [ ] **F3 · SubTask-Sheet schließt sich nicht nach Löschen der Hauptaufgabe**
  Wenn die Hauptaufgabe aus dem Edit-Dialog heraus gelöscht wird, bleibt das SubTask-Sheet geöffnet und referenziert eine nicht mehr existierende Aufgabe. Sheet sollte automatisch geschlossen werden — entweder über `dismiss()` im Lösch-Pfad oder über Reaktion auf `parentTask == nil`. Kandidat für 10719 als Beifang.

- [ ] **F4 · `markOpen` auf erledigter Wiederholung lässt Folge-Instanz bestehen**
  Szenario: Aufgabe mit Wiederholung ist erledigt, Scheduler hat bereits die nächste Instanz erstellt. Beim manuellen Zurücksetzen der erledigten Instanz auf `open` bleibt die Folge-Instanz bestehen → zwei sichtbare Instanzen derselben Wiederholungsgruppe. Beim Mehrfach-Abschluss greift `cleanupDuplicates()` korrekt, der Zwischenzustand bleibt aber für den Nutzer verwirrend.
  **Offene Frage:** Bug oder konzeptionell richtig? Aus Plan-vs-Realität-Perspektive ist die Folge-Instanz eigenständig (Plan führt). Aus UX-Perspektive ist der Doppel-Zustand verwirrend. Klärung nötig vor Umsetzung — gemeinsam mit B1 (Historie als Protokoll) denken, weil dort die Frage "was passiert mit erledigten Instanzen wenn man sie reaktiviert" generell auftaucht.

- [ ] **F5 · Kurzlebige Duplikate in Abgeschlossen-Sektion**
  Beim Erledigen einer Aufgabe erscheinen kurzzeitig zwei Einträge in der Abgeschlossen-Sektion, die nach wenigen Sekunden auf einen reduzieren. Vermutlich Race zwischen UI-Update, CloudKit-Sync und Scheduler-Folgeinstanz-Anlage. Sollte durch Build 10715 (TaskScheduler `@MainActor`) bereits mit-gelöst sein — Verifikation durch Beobachtung.

- [ ] **F6 · `unsafeForcedSync`-Console-Warning**
  Beim Simulate Background Fetch in 10715 wurde die Warning "Potential Structural Swift Concurrency Issue: unsafeForcedSync called from Swift Concurrent context" beobachtet. Kein Crash, kein Funktionsverlust. Quelle unklar — kann von SwiftData/CloudKit-internen Operationen, BGTaskScheduler oder MusicKit kommen. Eigene Diagnose-Etappe sinnvoll, kein Blocker.

- [ ] **F7 · `doesNotExpire`-Konzept-Review**
  Aktueller Zustand: "Erledigte entfernen" / "Alle zurücksetzen"-Buttons erscheinen ausschließlich bei Aufgaben mit `doesNotExpire = true`. Bei wiederkehrenden Aufgaben mit Teilaufgaben gibt es keine vergleichbare Bereinigung. Zu hinterfragen für 1.7.1:
  - Ist der Use-Case heute noch klar abgegrenzt von wiederkehrenden Aufgaben mit Teilaufgaben?
  - Findet der Nutzer die Buttons intuitiv (Sichtbarkeits-Bedingung verständlich)?
  - Sollte es bei wiederkehrenden Aufgaben einen anderen Mechanismus geben — etwa "Teilaufgaben-Vorlage zurücksetzen für nächste Wiederholung"?
  Konzept-Punkt, gehört thematisch zum Plan-vs-Realität-Block.

- [ ] **F8 · "Alle zurücksetzen" auch sichtbar wenn nichts zu reseten**
  Aktueller Code: `if showCleanupOptions { Button("Alle zurücksetzen") { ... } }` — der Reset-Button ist sichtbar, sobald eine doesNotExpire-Aufgabe Teilaufgaben hat, unabhängig davon ob die Teilaufgaben bereits offen sind. Ein Klick erzeugt dann einen leeren Historie-Snapshot ohne sinnvolle Wirkung.
  **Korrekte UX wäre:** `if (subTasks ?? []).contains { $0.status != .open } || status != .open { Button(...) { ... } }`. Kandidat für 10719 als Beifang.

- [ ] **F9 · Inline-Expand in Heute-Liste schwer auffindbar**
  Beim 10716-Test wurde festgestellt, dass die Inline-Expand-Funktion (Tap auf Aufgabe in der Heute-Liste klappt Subtasks unter der Zeile aus) nicht intuitiv erreichbar ist. Der primäre Tap-Pfad führt heute zum Sheet. UX-Frage für v1.7.1.

- [x] **F10 · `EditTaskView` hat eigene Subtask-Toggle-Implementierung ohne Sound** — erledigt in Build 10717 als Beifang von C-S38. `EditTaskView` nutzt jetzt `SubTaskRowView` statt der ursprünglich geplanten `InlineSubTaskRow` — `SubTaskRowView` war die passendere Komponente, weil sie Trash-Button und ContextMenu bereits enthält und in `SubTaskSheetView` schon eingesetzt wird.

- [ ] **F11 · `playCompletedRemoved`/`playTaskReset` möglicherweise nicht hörbar in EditTaskView**
  Beim Cleanup/Reset über den Editor-Pfad scheint der Sound nicht zu kommen, im Gegensatz zu den anderen zwei Pfaden, die dieselbe Extension nutzen. Da Chris den EditorView-Pfad im Alltag selten nutzt, ist die Reproduzierbarkeit unsicher. Konsistenz ist das Ziel, nicht akute Bug-Behebung. **Diagnose-Etappe in Build 10718** mit präzisem Schritt-für-Schritt-Verfahren und vorher abgestimmter Terminologie.

- [ ] **F12 · `syncStatus()` lockt fälschlicherweise**
  Beim 10717-Test entdeckt: Wenn alle Teilaufgaben einer Hauptaufgabe auf `done` (oder `skipped`) gesetzt werden, propagiert `syncStatus()` den abgeleiteten Status korrekt auf die Hauptaufgabe — setzt aber zusätzlich `isManualStatus = true`. Folge: Wenn der Nutzer anschließend die Teilaufgaben wieder auf `open` setzt, ignoriert `syncStatus()` die Änderung, weil die Hauptaufgabe gelockt ist. Die Hauptaufgabe bleibt auf `done` hängen.
  **Korrekt wäre:** `syncStatus()` darf das Lock-Flag NIE setzen — Locking ist ausschließlich Sache der `mark*(manually:)`-Methoden und `applyExpiry()`. Fix vermutlich einzeilig in `Models.swift`, aber Test-Aufwand: alle Status-Wechsel-Pfade neu durchspielen (Teilaufgaben → Hauptaufgabe und zurück, mit/ohne Lock).
  Kandidat für 10718 oder eigene Etappe; thematisch verwandt mit B1 (Historie als Protokoll) und F4 (markOpen lässt Folge-Instanz bestehen) — Plan-vs-Realität-Block.

### Methodische TODOs vor Diagnose-Etappen

- [x] **Begriffs-Glossar für UI-Hierarchie erstellen** — erledigt 29.04.2026 (vor Build 10718). Verbindlich abgestimmt zwischen Chris und Claude.

### Begriffs-Glossar (UI-Hierarchie)

Verbindliche Vokabeln für Diagnose-Etappen ab Build 10718.

**Tabs (Bottom-Navigation):**
- **Heute-Tab**, **Aufgaben-Tab**, **Historie-Tab**, **Einstellungen-Tab**

**Listen (jeweils im zugehörigen Tab):**
- **Heute-Liste** — die Liste in der Heute-Ansicht
- **Aufgaben-Liste** — die Liste im Aufgaben-Tab
- **Historie-Liste** — die Liste im Historie-Tab

**Modale Sheets:**
- **Teilaufgaben-Sheet** — das Sheet, das beim Tap auf eine Aufgabe in der Heute-Liste erscheint, wenn die Aufgabe Teilaufgaben hat (View: `SubTaskSheetView`)
- **Edit-Sheet** — das Sheet zum Bearbeiten einer Aufgabe (View: `EditTaskView`), erreichbar via Tap auf eine Aufgabe in der Aufgaben-Liste oder via Zahnrad-Icon im Teilaufgaben-Sheet
- **SubTaskEditor-Sheet** — das Sheet zum Bearbeiten einer einzelnen Teilaufgabe (Multi-Line-fähiger Texteditor)

**UI-Elemente in Listen-Zeilen:**
- **Inline-Expand-Bereich** — die ausklappbare Teilaufgaben-Anzeige unterhalb einer Aufgabenzeile in der Heute-Liste
- **Status-Auswahl** — das aufklappbare Element rechts an jeder Aufgabe (technisch ein `Menu`) mit den drei Status-Optionen Offen / Erledigt / Übersprungen, plus optional Cleanup/Reset-Aktionen für `doesNotExpire`-Aufgaben
- **Cleanup-/Reset-Menu** — der Block mit "Erledigte entfernen" und "Alle zurücksetzen". Erscheint zwei mal: (a) als Sektion innerhalb der Status-Auswahl in der Heute-Liste, (b) als eigenes Menu in der Toolbar des Teilaufgaben-Sheets (Pinsel-Icon links)

**Pfade (zur Charakterisierung von Diagnose-Szenarien):**
- **Heute-Pfad** — Aktion ausgelöst aus der Heute-Liste oder dem Teilaufgaben-Sheet
- **Edit-Sheet-Pfad** — Aktion ausgelöst aus dem Edit-Sheet (über Aufgaben-Tab → Aufgabe antippen → Cleanup im Menu)

### Reviews & Design

- [ ] **TemplatePaletteReview** – Durchsicht der Template-Farbpalette
- [ ] **KlangweltReview** – Durchsicht der vier Klangwelten
- [ ] **System-Klangwelt vollständig mit eigenen WAVs** — Erkenntnis aus 10716-Test: System-Töne fallen bei kurzen Aktionen (`playSubTaskDone` etc.) kaum hörbar aus. Soll Teil des KlangweltReview werden.
- [ ] **Hauptaufgaben-Design** – Abstimmung Website-Darstellung mit App-Design

### Onboarding-Verfeinerung (v1.7.x)

Konzept-Dokument: `MyCadenza_FeatureKonzepte_v1_7_x.md`

Zwei eng verzahnte Features für Neuanwender, die nach B-M2 (Build 10721) auf der dort entwickelten Sync-Erkennungs-Logik aufsetzen:

* **Default-Kategorien** — Vier Kategorien (Haushalt, Arbeit, Family & Friends, Gesundheit) werden beim Erst-Start auf leerer DB angelegt, jede mit eigener Klangwelt, Farbe und Icon. Sync-aware (CloudKit-Initial-Sync abwarten). Bestandsschutz für Nutzer von v1.6.1 oder früher.
* **Mustertemplate „MyCadenza einrichten"** — Lern-Container mit sechs Teilaufgaben, kategorisiert als „Haushalt" (Klangwelt Morgenwald), `doesNotExpire = true`, mit `isSampleData`-Marker für saubere Update-Logik. Beim Onboarding-Ende wird zusätzlich automatisch eine konkrete Aufgabe aus diesem Template generiert (Startzeit „jetzt", ohne Verfall, ohne Marker — normale User-Aufgabe). Visuell dezent als Onboarding-Begleiter markiert.

**Build-Skizze (nach Abschluss von 10724):**

* Sync-aware Erst-Start-Erkennung als wiederverwendbares Modul extrahieren
* Default-Kategorien anlegen (Datenmodell + Seed-Logik)
* Mustertemplate anlegen (Datenmodell + Inhalts-Definition)
* Onboarding-Anpassung — Screen 4 adaptiv (drei Varianten je nach Sync-Status), Sample-Aufgabe-Generation
* Visueller Onboarding-Hinweis (UI-Heuristik)

**Offene Designentscheidungen** (siehe Konzept-Dokument für Details):

* Konkrete Farbtöne und Icons der vier Kategorien
* Englische Übersetzungen der Kategorienamen
* Konkretes Onboarding-Begleiter-Icon
* Texte der sechs Teilaufgaben
* UX-Detail zur Abwählbarkeit von Defaults im Onboarding
### WAVs & Sound

- [ ] WAV-Review Cityflow + Horizont (ca. 30 Dateien ausstehend)
- [ ] ElevenLabs Prompt-Workshop für neue/erweiterte Sound Actions
- [ ] ElevenLabs Prompt-Länge testen (Tool generiert 10–20 % kürzer als spezifiziert → Prompts etwas länger ansetzen)

### Weitere Verbesserungen

- [ ] CloudKit-Sync für Settings (aktuell nur UserDefaults)
- [ ] In-App Sprachauswahl (statt iOS-Settings-Redirect)

---

## C) Feature Pool Zukunft

Ideen und Backlog – noch nicht konkret für ein Release geplant.

### Konzept-Etappen

- [ ] **Feinere Zeitstufen** (entdeckt 25.04.2026)
  Validity-Duration und Grace-Period um 5/15-Min-Werte ergänzen. Variante zu prüfen: Hybrid — 15 Min produktiv (legitimer UX-Wert für Mikro-Routinen), 5 Min DEBUG-only als Test-Hilfe. Gleichzeitig Debug-Helfer für sofortigen Verfall in SettingsView DEBUG-Block einbauen (Aufgabe als verfallen markieren ohne Wartezeit).

- [ ] **Strict Concurrency auf "Complete" umstellen** (nach v1.7.0-Release)
  Heute läuft das Projekt mit "Minimal" oder "Targeted" Strict Concurrency. Eine Umstellung auf "Complete" wird viele Warnings/Fehler auswerfen, weil Singletons (`MusicManager`, `PersistenceController`, `DataMigrator`, `DemoDataProvider`) systematisch annotiert werden müssen. Eigene Etappe wegen erwarteter Welle. Bereits annotiert (Stand 25.04.2026): `TaskScheduler` (Build 10715), `SoundManager` (war schon), `MainTask`-Extension für SubTask-Ops (Build 10716).

### Features

- [ ] **CarPlay-Integration** – Musiksteuerung aus dem Auto
- [ ] **Apple Reminders + Calendar via EventKit** – Mandanten-Verknüpfung, Wiedervorlagen, Fristen (mittelfristig)
- [ ] **Apple Contacts Integration** – Mandanten-Verknüpfung
- [ ] **Photo-Attachments auf Aufgaben** – alle 3 Ebenen (Haupt-/Teilaufgaben/Templates); Thumbnails lokal, Originale als CKAsset; Ablaufsystem mit Timer ab Task-Erledigung oder Task-Ablauf; PhotosPicker, SwiftData `@Model` mit `externalStorage`
- [ ] **Spotify-Integration** – MusicService-Protokoll ist bereits vorbereitet

---

## D) Website-Entwicklung

### Anwender-Dokumentation

- [ ] **Schicht 3 – Quick Start** – Kurzeinführung, DE + EN, auf GitHub Pages
- [ ] **Schicht 4 – Vollständige Dokumentation** – Detailreferenz, DE + EN, auf GitHub Pages

---

## Wochenplanung

**Aktueller Stand (29.04.2026):**
- v1.7.0 Build 10717 gepusht und getaggt
- 10715 in TestFlight, passive 24h-Crash-Verifikation läuft
- 14 von 108 Code-Review-Befunden erledigt
- F12 als neuer Befund entdeckt

**Vorschlag nächste Woche (in der Reihenfolge der Etappen):**

1. **Begriffs-Glossar erarbeiten** (vor 10718). 15 Min Konzept-Arbeit, eindeutige Bezeichnungen festklopfen.

2. **Build 10718 — F11-Diagnose** (EditTaskView Cleanup-Sound). Klein, präzise, mit dem neuen Glossar gut adressierbar.

3. **Build 10719 — Tote-Code-Etappe** (C-S5, C-S16, C-S18, C-S22, C-S29; C-M3). Beifang: F1, F3, F8 — falls umfangsverträglich. Klein-mittlere Etappe.

4. **Build 10720 — Localization-Cleanup**. Mittlere Etappe.

5. **Build 10721 — Import-Sperre B-M2**. Mittlere Etappe. **Letzter v1.7.0-Release-Blocker.**

6. **TestFlight-Sammelupload** der Builds 10716–10721 als ein gemeinsamer Test, dann App Store-Submission.

**Optional zwischendurch (Wochenende):**
- WAVs Cityflow + Horizont reviewen
- System-Klangwelt-WAVs vorbereiten

**Nach v1.7.0-Release:**
- v1.7.1 startet mit B1 (Historie als Protokoll), F4, F7, F9, F12 — Plan-vs-Realität-Block
- F12 (`syncStatus()` lockt fälschlicherweise) während 10717-Test entdeckt — Fix vermutlich einzeilig, aber Test-Aufwand spürbar; Kandidat für 10718 oder eigene Etappe
- TemplatePaletteReview, KlangweltReview, Hauptaufgaben-Design
- Strict Concurrency auf "Complete" als eigene Etappe

---

## Hinweise zum Arbeitsstil

**Build-Stage-Methodik:**
Jede Etappe erhält eine aufsteigende Buildnummer. Änderungen werden vor dem nächsten Schritt committet und getaggt, um jederzeit rollbackfähig zu bleiben. Eingeschobene Mini-Etappen erhalten Suffixe (z.B. 10712a, 10712b), damit die Hauptnumerierung kohärent bleibt.

**Git-Rhythmus pro Etappe:**
```
git status
git add -A
git commit -m "v1.7.X bXXXX: <Etappe>"
git tag v1.7.X-bXXXX
git push --follow-tags
```

**Terminal-Tipp:**
`git commit -a` erfasst nur *modifizierte*, nicht *untracked* Dateien. Immer `git add -A` davor — dann sind neue Dateien mit drin.

**Git-Editor-Default auf nano setzen (einmalig, bereits erledigt):**
```
git config --global core.editor "nano"
```

**Test-Ritus:**
Clean Build (⌘⇧K, dann ⌘B) → Simulator oder Gerät → Buildnummer in "Über MyCadenza" verifizieren → erst dann als "bestanden" markieren.

**Drei-Stufen-Test bei Crash-relevanten Etappen:**
Simulator → echtes iPhone (Developer-Build) → TestFlight. Sauberer Verifikations-Pfad mit klarer Trenndiagnostik.

**Referenzdokumente:**
- `MyCadenza_CodeReview_v1.7.0.md` – Detailliste aller Code-Review-Findings, Session-Log, Umsetzungsplan
- `Cadenza_Projektstruktur.md` – Projektstruktur & Bekannte Fallstricke
- `MyCadenza_Funktionsuebersicht.md` – App-Funktionsweise
- `MyCadenza_Zielarchitektur.md` – Strategischer Bezugsrahmen
- MyCadenza_FeatureKonzepte_v1_7_x.md` – Detailkonzepte für Onboarding-Verfeinerung (Default-Kategorien, Mustertemplate)
