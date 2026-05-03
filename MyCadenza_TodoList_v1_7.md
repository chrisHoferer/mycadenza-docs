# MyCadenza – ToDo-Liste

> Konsolidierte Roadmap nach Code Review v1.7.0
> Stand: 03.05.2026 · Builds 10718–10725 gepusht und getaggt — v1.7.0 Code-Etappen abgeschlossen · WhatsNewView-Update als Build 10726 geplant (letzter v1.7.0-Schritt vor App-Store-Submission) · F15 aufgenommen · Sound-Kaskaden in v1.7.1 von 10726 auf 10727 verschoben

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
- [x] **Build 10718** — Settings-UI: Demo & Präsentation für Endnutzer öffnen + "Was ist neu"-Button + DEBUG-Sektion aufräumen + Localization-Lücke fixen. Commit `24eebf5`, Tag `v1.7.0-b10718`. Eingeschoben — die ursprünglich für 10718 geplante F11-Diagnose ist entfallen, weil F11 widerlegt wurde (siehe unten).
- [x] **Build 10719** — Reset on Activation für Präsentationsmodus inkl. Hilfetext mit Verlust-Hinweis. Commit `031de78`, Tag `v1.7.0-b10719`. Eingeschoben — die ursprünglich für 10719 geplante Tote-Code-Etappe rückt auf 10720.
- [x] **Build 10720** — Tote-Code (C-S5/C-S16/C-S18/C-S22/C-S29 + C-M3) + F1/F3/F8-Beifang. Commit `9ad814c`, Tag `v1.7.0-b10720`. Diagnose-Phase mit Zwischen-Bericht vorab (Mapping aus Code, weil CodeReview-IDs ohne Detail-Beschreibung). Beifang aus dem Aufräumen: `private func deleteAllInstances(of:)` in `EditorView.swift` wurde durch die `.swipeActions`-Entfernung aufrufslos und ist mit entfernt. Vier Localization-Keys (`scheduleWithContext`-only) wurden von Xcode automatisch als `extractionState: stale` markiert — Cleanup gehört in 10721.
- [x] **Build 10721** — Localization-Cleanup (stale-keys + dynamische Keys + hardcoded Strings). Commit `8bb38c6`, Tag `v1.7.0-b10721`. Diagnose-Phase mit Zwischen-Bericht vorab. Drei Kategorien: (C) 4 echt-stale aus `scheduleWithContext` entfernt + 4 falsch-stale auf `extractionState: manual` (DEBUG-only Keys), (A) 26 Stellen auf `String(format:)` migriert (~15 unique Format-Keys, fast alle existierten bereits in xcstrings mit EN), (B) `AppMode.swift` `"Praesentation"` → `"Präsentation"` mit Umlaut. Erfreulicher Beifang: latenter Bug seit v1.6.1 — der Cleanup-Bestätigungsdialog zeigte im DE den Source-Key `"Cleanup Bestätigung 2"` als Müll-Text statt der eigentlichen Message. Heute beim 10721-Test entdeckt; DE+EN sauber befüllt mit Plural-Format-Wortlaut, Singular-Holprigkeit pragmatisch akzeptiert (kein `(n)`-Hack).
- [x] **Build 10722** — Import-Sperre B-M2 + iCloud-Status-Architektur (Observer + Header-Anzeige + SettingsView-Section + Sperren-Mechanik). Commit `36c422d`, Tag `v1.7.0-b10722`. **B-M2 final gelöst — letzter v1.7.0-Release-Blocker.**
  - Neue Klasse `CloudKitStatusObserver` (Singleton, `@MainActor`, `ObservableObject`) mit vier Mechanismen: UserDefaults-Marker, primär `eventChangedNotification`, sekundär `NSPersistentStoreRemoteChange` als Live-Signal, Timeouts (60s Initial, 5min Active-Sync). Initial-DB-Snapshot beim App-Start.
  - Wiederverwendbare UI-Bausteine in `CloudKitStatusView.swift`: `CloudKitStatusDot`, Status-Text-Helper, Modal-Hinweis-Logik.
  - **Header-Refactor in TodayView**: ProgressRingView von Hauptelement (40×40pt, rechts) zu Header-Element (32×32pt, links neben „Heute") verkleinert, immer sichtbar (auch bei 0/0). iCloud-Status-Bereich rechts neu angelegt mit Punkt + Mini-Text und Tap-zu-Settings-iCloud-Section (ScrollViewReader + `.id`-Anker, TabView-Selection-Binding in ContentView).
  - **Wiederhersteller-Banner aus TodayView entfernt** — Status-Bereich rechts im Header übernimmt diese Information; bei `.initialContact` mit leerer DB pulsiert der Punkt blau mit „Verbindung…", ein Tap führt direkt zur Settings-iCloud-Section.
  - Sperren-Mechanik: Daten-Export und beide Import-Modi `.disabled(!isImportAllowed)` während Sync, Section-Footer-Hinweistext. Duplikate-Bereinigen + Alle-Daten-zurücksetzen nicht gesperrt (kollidieren nicht mit Sync).
  - Tagesbericht-Modal mit kontext-sensitivem Status-Hinweis-Block (gelb für Fehler, blau für Wiederhersteller, grau dezent sonst).
  - 23 neue Localization-Keys DE+EN als `extractionState: manual`.
  - **Test-Iteration:** Beim ersten Test State-Inkonsistenz Header ↔ Settings entdeckt — Ursache war `@StateObject` mit Singleton (Apple-Anti-Pattern) plus `@State`-basierte `.repeatForever()`-Animation, die bei Status-Wechsel nicht sauber stoppte. Fix: `@StateObject` raus, direkter `.environmentObject(.shared)`-Inject; `CloudKitStatusDot` auf `TimelineView(.animation)` umgebaut, `if shouldPulse { PulsingCircle } else { Circle }`-Pattern (Subview wird beim Status-Wechsel komplett ausgetauscht, kein lingering Animation-State).
  - **Farbe** des Status-Punkts auf `Color(.systemGreen)` für `.synced` (Apple-System-Grün, hell und frisch). Der Fortschrittsring nutzt noch das alte matte Grün — das adressiert das spätere Design-Review.
- [↗] **Build 10723** — Sound-Kaskaden (B-S2) → **verschoben nach v1.7.1, neue Buildnummer 10727**. Begründung: hängt von KlangweltReview-Entscheidung ab, heute nicht als reine Ausführungs-Etappe lösbar.
- [ ] **Build 10724** — MusicPlayerView Konsolidierung (C-S43, C-S44, C-S45, C-S46)
- [x] **Build 10725** — SoundManager preview-API (C-S42) + tagKomplett-Doppelton-Duplikat eliminiert (C-A45 Beifang) + CHANGELOG-Sweep. Commit `d196ea2`, Tag `v1.7.0-b10725`. Drei Stränge in atomic commit: (1) `SoundManager.preview(action:world:volume:)` neu, übergeht `isEnabled` und Haptic, volume explizit; SoundtestView delegiert komplett, eigener `audioPlayer`-State entfernt. (2) Doppelton-Duplikat zwischen SoundManager und SoundtestView durch Delegation aufgelöst. (3) CHANGELOG.md im App-Repo nachgepflegt: 10717-Eintrag korrigiert (war fälschlich „F3 SubTask-Sheet dismiss", korrekt ist C-S38/C-S39/F10 vom 29.04.), Builds 10718–10725 chronologisch nachgetragen. Test-Befund F15 entdeckt (siehe v1.7.1-Block).
- [ ] **Build 10726** — WhatsNewView-Inhalt für v1.7.0 + Layout-Update (Option C). Vier thematische Features: iCloud-Status auf einen Blick · Frischer Heute-Bildschirm · Demo & Präsentation für alle · Neuerungen jederzeit nachlesen. Layout-Wechsel: Versions-Info wandert vom Header in einen visuell umrahmten Block mit Gold-Tint und Caption-Überschrift „Neu in Version 1.7.0". Schluss-Satz „Stabilitäts- und Detailverbesserungen unter der Haube" vor dem Button. Letzter Code-Schritt vor App-Store-Submission v1.7.0.

### Offene Code-Quality-Items

- [x] `@Relationship inverse:` Attribute in `Models.swift` und `TaskTemplate.swift` ergänzt
- [ ] `cleanupDuplicates()` – jetzt preservation-score-basiert, bleibt flagged (Review)
- [ ] `processAllTasks()` – Debounce- und ScenePhase-Handler-Cleanup finalisieren
- [x] xcstrings – verbleibende Artefakte bereinigt (in Build 10721 mitgemacht: 4 stale entfernt, 4 falsch-stale auf `manual`, leerer `"Praesentation"`-Eintrag mit raus)

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

- [x] **F1 · Abgeschlossen-Sektion Darstellung schärfen** — erledigt in Build 10720 als Beifang. `TodayTaskRowView` bekommt `isInCompletedSection: Bool = false`, in der Abgeschlossen-Sektion: Farbbalken grün bei `.done` / gold-gedämpft (60% Opazität) bei `.skipped`, Status-Haken grün bei `.done`, Reorder-Handle via `.moveDisabled(true)` weg. Keine eigene Zeilenvariante — konditional in `TodayTaskRowView` über Sektionskontext durchgereicht.

- [x] **F2 · `markOpen` soll Teilaufgaben durchreichen** — erledigt in Build 10712c. `.open`-Case in `applyStatusToSubTasks()` setzt jetzt alle `done`/`skipped`-Teilaufgaben auf `open` zurück. `markOpen` entsperrt grundsätzlich (`isManualStatus = false`), symmetrisch zu `markDone`/`markSkipped`.

- [x] **F3 · SubTask-Sheet schließt sich nicht nach Löschen der Hauptaufgabe** — erledigt in Build 10720 als Beifang. Variante A umgesetzt: `EditTaskView` bekommt optionalen `onDelete: (() -> Void)?`-Callback, der nach erfolgtem Delete vor dem eigenen `dismiss()` aufgerufen wird. `SubTaskSheetView` reicht beim Edit-Aufruf das eigene `dismiss` durch — Cascade-Schließen beider gestapelter Sheets verifiziert.

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

- [x] **F8 · "Alle zurücksetzen" auch sichtbar wenn nichts zu resetten** — erledigt in Build 10720 als Beifang. Drei Stellen angepasst (EditTaskView, TodayTaskRowView, SubTaskSheetView): Reset-Button nur sichtbar, wenn `(subTasks ?? []).contains { $0.status != .open } || status != .open`. In `SubTaskSheetView` zusätzlich das paintbrush-Toolbar-Menü versteckt, wenn weder „Erledigte entfernen" noch „Alle zurücksetzen" greifen.

- [ ] **F9 · Inline-Expand in Heute-Liste schwer auffindbar**
  Beim 10716-Test wurde festgestellt, dass die Inline-Expand-Funktion (Tap auf Aufgabe in der Heute-Liste klappt Subtasks unter der Zeile aus) nicht intuitiv erreichbar ist. Der primäre Tap-Pfad führt heute zum Sheet. UX-Frage für v1.7.1.

- [x] **F10 · `EditTaskView` hat eigene Subtask-Toggle-Implementierung ohne Sound** — erledigt in Build 10717 als Beifang von C-S38. `EditTaskView` nutzt jetzt `SubTaskRowView` statt der ursprünglich geplanten `InlineSubTaskRow` — `SubTaskRowView` war die passendere Komponente, weil sie Trash-Button und ContextMenu bereits enthält und in `SubTaskSheetView` schon eingesetzt wird.

- [x] **F11 · `playCompletedRemoved`/`playTaskReset` möglicherweise nicht hörbar in EditTaskView** — verifiziert 02.05.2026, Bug nicht reproduzierbar. Sound im Edit-Sheet ist hörbar, F11 widerlegt. **Mini-Befund (an WAV-Review/KlangweltReview angehängt):** `.erledigteEntfernt` fällt in einer Klangwelt offenbar auf `.teilaufgabeUebersprungen` zurück (Fallback-Mechanik in `SoundManager` Z. 113). Konkrete WAV-Lücke beim Klangwelten-Review prüfen.

- [ ] **F12 · `syncStatus()` lockt fälschlicherweise**
  Beim 10717-Test entdeckt: Wenn alle Teilaufgaben einer Hauptaufgabe auf `done` (oder `skipped`) gesetzt werden, propagiert `syncStatus()` den abgeleiteten Status korrekt auf die Hauptaufgabe — setzt aber zusätzlich `isManualStatus = true`. Folge: Wenn der Nutzer anschließend die Teilaufgaben wieder auf `open` setzt, ignoriert `syncStatus()` die Änderung, weil die Hauptaufgabe gelockt ist. Die Hauptaufgabe bleibt auf `done` hängen.
  **Korrekt wäre:** `syncStatus()` darf das Lock-Flag NIE setzen — Locking ist ausschließlich Sache der `mark*(manually:)`-Methoden und `applyExpiry()`. Fix vermutlich einzeilig in `Models.swift`, aber Test-Aufwand: alle Status-Wechsel-Pfade neu durchspielen (Teilaufgaben → Hauptaufgabe und zurück, mit/ohne Lock).
  Kandidat für 10718 oder eigene Etappe; thematisch verwandt mit B1 (Historie als Protokoll) und F4 (markOpen lässt Folge-Instanz bestehen) — Plan-vs-Realität-Block.

- [ ] **F13 · Reset einer Teilaufgabe auf `.open` lässt Hauptaufgaben-Status auf `.done` stehen**
  Beim 10720-Test reproduziert: Aufgabe ohne Teilaufgaben angelegt, Teilaufgabe nachträglich hinzugefügt, Teilaufgabe als erledigt markiert (Hauptaufgabe wurde automatisch mitskaliert auf `.done` und in Abgeschlossen verschoben), Teilaufgabe wieder auf `.open` zurückgesetzt — Hauptaufgabe blieb aber auf `.done`.
  **Vermutete Ursache:** `syncStatus()` läuft nicht beim Wechsel einer einzelnen Teilaufgabe, oder `isManualStatus` blockiert (siehe F12 — vermutlich derselbe Lock-Mechanismus, der bei Teilaufgaben-Änderung greift).
  v1.7.1. Sehr wahrscheinlich gleicher Fix-Pfad wie F12.

- [ ] **F14 · `AddTaskView` braucht spürbar lang, bis das Textfeld fokussierbar ist**
  Beim 10720-Test beobachtet: Nach Tap auf „Aufgabe hinzufügen" dauert es spürbar, bis das erste Textfeld den Fokus annimmt und die Tastatur erscheint. Ursache offen.
  **Lade-Profil zu prüfen:** Defaults-Berechnung beim View-Init (Standard-Gültigkeitsdauer, Verfallfrist, Standard-Wiederholung), Sheet-Animation und FocusState-Timing, mögliche iCloud-Sync-Side-Effects beim Anlegen einer neuen `MainTask`-Instanz, Kategorie-Liste-Fetch.
  v1.7.1.

- [ ] **F15 · Lautstärke-Differenzierung der drei Stufen zu schwach**
  Beim 10725-Test entdeckt (03.05.2026). Aktuelle Werte `quiet=0.3 / normal=0.6 / loud=1.0` sind linear gestuft, Lautstärke-Wahrnehmung ist aber logarithmisch — der Unterschied zwischen Leise und Normal fühlt sich kleiner an als zwischen Normal und Laut. Bei kurzen Tönen (0,5-Sek-Klicks) ist die Differenz besonders schwer wahrnehmbar.
  **Lösungsansatz:** Spreizung wie `0.15 / 0.5 / 1.0` erwägen (logarithmischer). Plus: bei System-Klangwelt ist der Volume-Picker technisch wirkungslos, weil `AudioServicesPlayAlertSound` die Volume-Property ignoriert — UI-Hinweis im Footer der System-Klangwelt sinnvoll. Datei: `SoundManager.swift` Z. 171-177 (`SoundVolume.volumeLevel`), plus `SoundtestView` für den Footer-Hinweis.

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
- [ ] **App-weite Farbpalette-Konsistenz** (Vermerk aus 10722) — Beim 10722-Test wurde der `CloudKitStatusDot` auf `Color(.systemGreen)` umgestellt (hell, frisch, Apple-System-Standard). Der Fortschrittsring (`ProgressRingView`) zeigt aber noch das alte matte Grün (`Color.green`/Hex-Variante). Im Rahmen des Design-Reviews gemeinsam entscheiden, welche Grün-Töne app-weit Verwendung finden — Status-Indikatoren, Fortschrittsring, „erledigt"-Markierungen evtl. unterschiedlich. Heute bewusst nicht halbgar mitgeändert.
- [ ] **KlangweltReview** – Durchsicht der vier Klangwelten. **Mini-Befund aus F11-Verifikation (02.05.2026):** `.erledigteEntfernt` fällt in einer Klangwelt auf `.teilaufgabeUebersprungen` zurück (Fallback in `SoundManager` Z. 113). Konkrete WAV-Lücke beim Review identifizieren und schließen.
- [ ] **System-Klangwelt vollständig mit eigenen WAVs** — Erkenntnis aus 10716-Test: System-Töne fallen bei kurzen Aktionen (`playSubTaskDone` etc.) kaum hörbar aus. Soll Teil des KlangweltReview werden.
- [ ] **Hauptaufgaben-Design** – Abstimmung Website-Darstellung mit App-Design

### Doku-Pflege

- [ ] **SwiftUI-Konventionen-Sammlung in `Cadenza_Projektstruktur.md` ergänzen**
  Bei Reduktion der `CLAUDE.md` am 03.05.2026 (App-Repo, Commit `0e56098`)
  sind technische Fallstricke ohne dokumentierte Heimat sichtbar geworden.
  In den vorhandenen „Bekannte Fallstricke"-Abschnitt der Projektstruktur
  übernehmen:
  - `.swipeActions` + `.editMode(.constant(.active))` inkompatibel
  - `.navigationTitle` Large-Title-Verhalten
  - `Button` in Section Header benötigt `.buttonStyle(.plain)`
  - `Spacer()` in Buttons benötigt `.contentShape(Rectangle())` für
    klickbaren Gesamtbereich
  - Audio Session `.ambient` als gewählte Kategorie für Klangwelten
  - weitere Konventionen, die beim Lesen sichtbar werden
  Klein, kombinierbar mit der nächsten Doku-Pflege-Welle.

### Onboarding-Verfeinerung (v1.7.x)

Konzept-Dokument: `MyCadenza_FeatureKonzepte_v1_7_x.md`

Zwei eng verzahnte Features für Neuanwender, die auf der in Build 10722 (B-M2) entwickelten Sync-Erkennungs-Logik aufsetzen — `CloudKitStatusObserver` mit Initial-DB-Snapshot lässt sich für Erst-Start-Erkennung wiederverwenden:

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

- [ ] **Build 10727** — Sound-Kaskaden (B-S2, ursprünglich als 10723 geplant, am 03.05.2026 verschoben). `audioPlayer` als einzelne Instanzvariable in `SoundManager.swift` (Z. 208) — jeder `play`-Call überschreibt den vorherigen. Umsetzungsentscheidung hängt vom KlangweltReview ab: Player-Pool zur Erlaubnis von Kaskaden, oder bewusster Abbruch des laufenden Sounds. Etappe nach KlangweltReview einplanen.
- [ ] WAV-Review Cityflow + Horizont (ca. 30 Dateien ausstehend). **Mini-Befund aus F11-Verifikation (02.05.2026):** Querbezug zur fehlenden `.erledigteEntfernt`-WAV in einer Klangwelt — siehe KlangweltReview oben.
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

- [ ] **Plural-Variations für Format-Strings** (Vermerk aus 10721)
  Heute werden Format-Strings mit `%lld` ohne Plural-Differenzierung gespeichert (z. B. „%lld erledigte Teilaufgaben werden entfernt." — bei 1 holprig, aber pragmatisch akzeptiert). Apple-`xcstrings` unterstützt Plural-Variations via `variations`/Genitiv-Marker; nötig spätestens bei Sprachen mit komplexer Pluralmorphologie (RU, AR, PL). Folge-Item für die Strategiesitzung nach v1.7.1. Kein dringender Punkt, solange wir bei DE/EN bleiben.

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

**Aktueller Stand (02.05.2026 abends):**
- **v1.7.0 funktional komplett — alle Release-Blocker gelöst.** Builds 10718 + 10719 + 10720 + 10721 + 10722 gepusht und getaggt — fünf Code-Builds an einem Tag, alle einzeln getestet, alle einzeln rollback-fähig.
- 18+ von 108 Code-Review-Befunden erledigt (5 C-S + 1 C-M Tote-Code in 10720 + B-M2 in 10722, plus die schon vorhandenen 14). Localization-Cluster in 10721 als Cluster aufgelöst.
- B-M2 (Import-Sperre bei aktivem CloudKit-Sync) ist final gelöst — substantielle Erweiterung gegenüber dem ursprünglichen Konzept zu einer vollen iCloud-Status-Architektur (Observer + UI-Anzeigen + Sperren-Mechanik). Wiederhersteller-Fall transparent für den Nutzer.
- F1, F3, F8 erledigt (in Build 10720 mitgelöst)
- F11 verifiziert und widerlegt (Sound im Edit-Sheet hörbar) — Mini-Befund zu `.erledigteEntfernt`-Fallback an KlangweltReview/WAV-Review angehängt
- F12 (`syncStatus()`-Lock) bleibt offener Befund für v1.7.1
- F13 + F14 als neue UX-Findings v1.7.1 aufgenommen (beim 10720-Test entdeckt)
- Latentbug seit v1.6.1 (Cleanup-Bestätigung zeigte Source-Key statt Übersetzung) in 10721 mit-eliminiert
- Plural-Variations + App-weite Farbpalette-Konsistenz als Folge-Items im C-Pool

**Nächste Schritte sind keine Builds mehr, sondern Release-Vorbereitung:**

1. **WhatsNewView-Inhalt für v1.7.0** — Feature-Liste in `OnboardingView.swift` aktualisieren (Demo & Präsentation für Endnutzer, „Was ist neu"-Button, iCloud-Status-Anzeige, Cleanup-Verbesserungen).
2. **CHANGELOG-Pflege** — alle fünf heutigen Builds plus die vorherigen v1.7.0-Builds dokumentieren.
3. **TestFlight-Sammelupload** der Builds 10716–10722 als ein gemeinsamer Test.
4. **App-Store-Submission** nach erfolgreichem TestFlight-Test.

**Optional zwischendurch (Wochenende):**
- WAVs Cityflow + Horizont reviewen (inkl. `.erledigteEntfernt`-Lücke aus F11-Verifikation)
- System-Klangwelt-WAVs vorbereiten

**Nach v1.7.0-Release:**
- v1.7.1 startet mit B1 (Historie als Protokoll), F4, F7, F9, F12, F13 — Plan-vs-Realität-Block
- F12 + F13 (`syncStatus()`/Lock-Mechanismus) — vermutlich gleicher Fix-Pfad, Test-Aufwand spürbar (alle Status-Wechsel-Pfade durchspielen)
- F14 (`AddTaskView`-Fokus-Latenz) — Diagnose-Etappe, Lade-Profil prüfen
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
