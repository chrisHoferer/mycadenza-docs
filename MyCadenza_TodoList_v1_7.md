# MyCadenza – ToDo-Liste

> Konsolidierte Roadmap nach Code Review v1.7.0
> Stand: 06.05.2026 · **v1.7.0 (Build 10726) im App Store live seit 04.05.2026** · F12 + F13 am 04.05.2026 verifiziert und geschlossen (beide nicht reproduzierbar, vermutlich implizit gefixt durch setStatus-Helper aus 10711 und Subtask-Operations-Konsolidierung aus 10716/10717) · v1.7.1-Build-Reihenfolge fixiert: 10727 F15 (Lautstärke), 10728 Sound-Kaskaden (wartet weiterhin auf KlangweltReview). Doku-Korrektur 04.05.2026: Build 10724 (MusicPlayerView-Konsolidierung) ist seit 03.05.2026 als v1.7.0 b10724 ausgeliefert — die kurzzeitige Verschiebung nach v1.7.1 war Irrtum, jetzt korrigiert. **Doku-Migration 04.05.2026:** Repo restrukturiert mit Subordnern `Klangwelt/`, `Designwelt/`, `_eingefroren/`; Klangwelt-/CodeReview-/Zielarchitektur-Dateien verschoben und kurzpräfix-umbenannt (MCK_/MCD_); Skeletons für Klangwelt- und Designwelt-Säule angelegt; siehe Doku-Struktur-Block direkt unten. **Klangwelt-Inhalts-Befüllung 05.05.2026:** `Klangwelt/MCK_Manifest.md` und `Klangwelt/MCK_Chartas.md` mit finalen Inhalten befüllt (Skeleton-Status verlassen) — `MCK_Methodik.md` bleibt vorerst Skeleton. **Mini-Korrekturen 05.05.2026:** Zwei punktuelle Eingriffe in `Klangwelt/MCK_Inventur.md` — Apple-Music-Eintrag (`MusicManager.swift` Z. 91) aus Sektion 1 nach Sektion 3 verschoben (thematische Hoheit: MusicKit ist paralleles Audio-System, kein Klangwelt-Sound), Annotation zur Disabled-Architektur in Sektion 2 ergänzt (Validierungsfehler-Stille ist bewusst, weil Speichern-Buttons via `.disabled(...)` gesperrt sind). **Sound-Verdienst-Beschluss 06.05.2026:** Sektion 5 in MCK_Inventur.md angelegt — sechs neue SoundActions identifiziert (`.aktionRegistriert`, `.aktionAbgeschlossen`, `.warnungVorAktion`, `.updateVerfuegbar`, `.syncCompleted`, `.syncFailed`), Audio-Session-Konfiguration `.ambient + .duckOthers` mit Volume-Resolution beschlossen. WAVs werden nach Methodik-Befüllung komplett neu generiert. **Klangwelt-Methodik vollständig befüllt 06.05.2026:** `Klangwelt/MCK_Methodik.md` aus Skeleton-Status (243 B) in 9-Sektionen-Vollfassung (~539 Zeilen) überführt — Zweck/Bezugsrahmen, Sound-Verdienst-Heuristik, Sound-Längen-Konvention, Aktions-Klassifikation, Audio-Session-Konfiguration, ElevenLabs-Workflow, Iterations-Methodik, Cluster A Dirty-Flag-Logik, Haptik-Annex. Drei TODO-Items abgeleitet: Haptik-Säulen-Frage, Code-Etappe Audio-Session-Robustheit, MCK_Bewertung.xlsx-Restrukturierung. **MCK_Bewertung.xlsx restrukturiert 06.05.2026 (`27d90db`):** Sheet-Struktur auf 16-Aktionen-Architektur umgebaut — Inventur (umbenanntes „Bewertung") + 5 Welt-Sheets (Morgenwald, Salon, Cityflow, Horizont, System) + erweiterte Legende; 80 Sound-Slots (16 × 5); Dateinamen-Schema `<welt-2b>_<klang-2b><slot-2-stell>.wav` (Beispiel: `sa_pn04.wav` = Salon, Piano, Slot 04 = hauptaufgabeKomplett). TODO-Item „MCK_Bewertung.xlsx restrukturieren" damit erledigt. **Klangwelt-Umsetzungs-Phasen-Plan 06.05.2026:** TodoList-Sektion „WAVs & Sound" zu „Klangwelt-Umsetzung" umstrukturiert — fünf Phasen (Test, Salon-Produktion, übrige Welten, Code-Änderungen, Test+Evaluation am Gerät), Pflichtlektüre-Block plus zentrale Beschlüsse aus der Methodik-Etappe als Wiedereinstiegspunkt. Klangwelt-relevante Items aus Reviews & Design, Build-Etappen v1.7.1 (Build 10728 mit Querverweis), Wochenplanung („Nächste Code-Etappe(n)" → „Nächste Etappe(n)") und v1.7.1-Strategischer-Übersicht konsolidiert auf den Phasen-Plan.

---

## Doku-Struktur (Stand 04.05.2026)

Das Doku-Repo ist seit 04.05.2026 in drei Säulen + Top-Level organisiert:

| Pfad | Inhalt | Präfix |
|---|---|---|
| `Klangwelt/` | Akustische Säule: Manifest, Methodik, Chartas, Schema, Prompts, Inventur, Bewertung | `MCK_` |
| `Designwelt/` | Visuelle Säule: Manifest, Methodik, Farben, Typografie, Komponenten, Iconographie, Befunde | `MCD_` |
| `_eingefroren/` | Snapshot-Archiv: nicht mehr aktiv gepflegte Dokumente, die einen Stand festhalten (Zielarchitektur, CodeReview v1.7.0) | — |
| Top-Level | Organisatorische Doku: TodoList, Roadmap, Projektstruktur, Funktionsuebersicht, FeatureKonzepte, README, Gestaltung (Brücke Klangwelt ↔ Designwelt) | — |

Präfix-Bedeutung:
- `MCK_` = MyCadenza Klangwelt
- `MCD_` = MyCadenza Designwelt

Skeleton-Dokumente (Stand-Datum 04.05.2026, Inhalt folgt in späterer Etappe):
- `MyCadenza_Gestaltung.md` (Brücke)
- `Klangwelt/MCK_Methodik.md` (MCK_Manifest.md und MCK_Chartas.md am 05.05.2026 befüllt — Skeleton-Status verlassen)
- `Designwelt/MCD_Manifest.md`, `MCD_Methodik.md`, `MCD_Farben.md`, `MCD_Typografie.md`, `MCD_Komponenten.md`, `MCD_Iconographie.md`, `MCD_Befunde.md`

---

## Struktur

Die Aufgaben sind in vier Blöcke aufgeteilt:

| Block | Inhalt | Leitgedanke |
|---|---|---|
| **A) v1.7.0** | Bugfix & Korrektheit | Was kaputt ist, reparieren + Semantik-Korrektheit |
| **B) v1.7.1** | Code-Qualität, Design, Plan-vs-Realität | Was strukturell besser werden soll |
| **C) Feature Pool Zukunft** | Noch nicht geplante Features | Ideen & Backlog |
| **D) Website** | Anwender-Dokumentation | Schicht 3 + 4 |

> **Hinweis zur Release-Strategie:** Builds 10713–10726 sind in v1.7.0 gelandet (App Store Release am 04.05.2026 mit Build 10726). Ab Build 10727 beginnt formal der v1.7.1-Train, beginnend mit F15 (Lautstärke-Differenzierung).

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
- [↗] **Build 10723** — Sound-Kaskaden (B-S2) → **verschoben nach v1.7.1, neue Buildnummer 10728**. Begründung: hängt von KlangweltReview-Entscheidung ab, heute nicht als reine Ausführungs-Etappe lösbar.
- [x] **Build 10724** — MusicPlayerView Konsolidierung (C-S43, C-S44, C-S45, C-S46) — MusicArtwork-Komponente + TrackSource-Refactor. Commit `e597629`, Tag `v1.7.0-b10724` (03.05.2026 08:08). MusicPlayerView.swift -108/+89, MusicManager.swift +9. Bestandteil der App-Store-Version v1.7.0 (Build 10726).
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

### Build-Etappen v1.7.1

Reihenfolge fixiert am 04.05.2026 nach v1.7.0-Release. Die anderen v1.7.1-Findings (F4, F5, F6, F7, F9, F14, B1, Reviews) bleiben weiter ohne feste Buildnummer im Backlog.

- [x] **Build 10727** — F15 (Lautstärke-Differenzierung der Volume-Stufen). Commit `8fdd923`, Tag `v1.7.1-b10727`. Erste v1.7.1-Code-Etappe. Volume-Werte logarithmisch gespreizt (0.15/0.5/1.0), zweizeiliger Footer-Hinweis bei System-Klangwelt in `SoundtestView`, konditionaler Volume-Wirkungslos-Hinweis in `SettingsView` wenn `dayBookendSoundWorld == .system`. MARKETING_VERSION auf 1.7.1.
- [ ] **Build 10728** — Sound-Kaskaden (B-S2). Inhaltlich aufgegangen im Klangwelt-Umsetzungs-Phasen-Plan (Phase 4f, siehe Sektion „Klangwelt-Umsetzung"). Konkrete Buildnummer wird bei Etappenplanung der Phase 4 festgelegt — Phase 4 kann als ein oder mehrere Builds realisiert werden. Vormals Build 10723 in v1.7.0.

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

- [x] **F12 · `syncStatus()` lockt fälschlicherweise**
  Beim 10717-Test entdeckt: Wenn alle Teilaufgaben einer Hauptaufgabe auf `done` (oder `skipped`) gesetzt werden, propagiert `syncStatus()` den abgeleiteten Status korrekt auf die Hauptaufgabe — setzt aber zusätzlich `isManualStatus = true`. Folge: Wenn der Nutzer anschließend die Teilaufgaben wieder auf `open` setzt, ignoriert `syncStatus()` die Änderung, weil die Hauptaufgabe gelockt ist. Die Hauptaufgabe bleibt auf `done` hängen.
  **Korrekt wäre:** `syncStatus()` darf das Lock-Flag NIE setzen — Locking ist ausschließlich Sache der `mark*(manually:)`-Methoden und `applyExpiry()`. Fix vermutlich einzeilig in `Models.swift`, aber Test-Aufwand: alle Status-Wechsel-Pfade neu durchspielen (Teilaufgaben → Hauptaufgabe und zurück, mit/ohne Lock).
  Kandidat für 10718 oder eigene Etappe; thematisch verwandt mit B1 (Historie als Protokoll) und F4 (markOpen lässt Folge-Instanz bestehen) — Plan-vs-Realität-Block.
  **Verifiziert 04.05.2026, im Code widerlegt:** `syncStatus()` schreibt nur `statusRaw` und `lastExecutedDate`, fasst `isManualStatus` nie an. Globale Suche ergab: `isManualStatus` wird ausschließlich im `init`, im privaten `setStatus(_:manually:)`-Helper und in `cleanupCompletedSubTasks` (auf `false`) geschrieben. Keine versteckte Lock-Stelle. Vermutlich implizit mit-gelöst durch die `setStatus(_:manually:)`-Helper-Einführung in Build 10711.

- [x] **F13 · Reset einer Teilaufgabe auf `.open` lässt Hauptaufgaben-Status auf `.done` stehen**
  Beim 10720-Test reproduziert: Aufgabe ohne Teilaufgaben angelegt, Teilaufgabe nachträglich hinzugefügt, Teilaufgabe als erledigt markiert (Hauptaufgabe wurde automatisch mitskaliert auf `.done` und in Abgeschlossen verschoben), Teilaufgabe wieder auf `.open` zurückgesetzt — Hauptaufgabe blieb aber auf `.done`.
  **Vermutete Ursache:** `syncStatus()` läuft nicht beim Wechsel einer einzelnen Teilaufgabe, oder `isManualStatus` blockiert (siehe F12 — vermutlich derselbe Lock-Mechanismus, der bei Teilaufgaben-Änderung greift).
  v1.7.1. Sehr wahrscheinlich gleicher Fix-Pfad wie F12.
  **Verifiziert 04.05.2026 live in der App-Store-Version:** Aufgabe ohne Teilaufgaben angelegt (in EditorView), Teilaufgabe nachträglich im SubTaskSheet hinzugefügt, in TodayView als erledigt markiert → Hauptaufgabe sprang korrekt auf `.done` und wanderte in Abgeschlossen. Teilaufgabe wieder auf `.open` zurückgesetzt → Hauptaufgabe sprang ebenfalls auf `.open` zurück und erschien wieder unter „Anstehend" (Beginnzeit noch nicht erreicht). Reset-Pfad funktioniert sauber. Keine Code-Änderung nötig.

- [ ] **F14 · `AddTaskView` braucht spürbar lang, bis das Textfeld fokussierbar ist**
  Beim 10720-Test beobachtet: Nach Tap auf „Aufgabe hinzufügen" dauert es spürbar, bis das erste Textfeld den Fokus annimmt und die Tastatur erscheint. Ursache offen.
  **Lade-Profil zu prüfen:** Defaults-Berechnung beim View-Init (Standard-Gültigkeitsdauer, Verfallfrist, Standard-Wiederholung), Sheet-Animation und FocusState-Timing, mögliche iCloud-Sync-Side-Effects beim Anlegen einer neuen `MainTask`-Instanz, Kategorie-Liste-Fetch.
  v1.7.1.

- [x] **F15 · Lautstärke-Differenzierung der drei Stufen zu schwach**
  Beim 10725-Test entdeckt (03.05.2026). Aktuelle Werte `quiet=0.3 / normal=0.6 / loud=1.0` sind linear gestuft, Lautstärke-Wahrnehmung ist aber logarithmisch — der Unterschied zwischen Leise und Normal fühlt sich kleiner an als zwischen Normal und Laut. Bei kurzen Tönen (0,5-Sek-Klicks) ist die Differenz besonders schwer wahrnehmbar.
  **Lösungsansatz:** Spreizung wie `0.15 / 0.5 / 1.0` erwägen (logarithmischer). Plus: bei System-Klangwelt ist der Volume-Picker technisch wirkungslos, weil `AudioServicesPlayAlertSound` die Volume-Property ignoriert — UI-Hinweis im Footer der System-Klangwelt sinnvoll. Datei: `SoundManager.swift` Z. 171-177 (`SoundVolume.volumeLevel`), plus `SoundtestView` für den Footer-Hinweis.
  **Erledigt mit Build 10727 (04.05.2026), Commit `8fdd923`, Tag `v1.7.1-b10727`.** Volume-Werte auf 0.15/0.5/1.0 gesetzt, Footer-Hinweis bei System-Klangwelt in `SoundtestView` erweitert (zweite Zeile zur Volume-Wirkungslosigkeit), konditionaler Hinweis in `SettingsView` ergänzt wenn `dayBookendSoundWorld == .system`. MARKETING_VERSION beider Targets auf 1.7.1 hochgezogen.

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
- [ ] **Hauptaufgaben-Design** – Abstimmung Website-Darstellung mit App-Design
- [ ] **Haptik-Säulen-Frage überprüfen** — Heute ist Haptik als Annex der Klangwelt geführt (siehe `Klangwelt/MCK_Methodik.md` Sektion 9). Bei späteren Erweiterungen (welt-spezifische Haptik-Profile, Accessibility-Layer, eigenständige Haptik-Designs unabhängig vom Sound) ist die Säulen-Frage neu zu bewerten. Zeitpunkt offen — vermutlich nach Klangwelt-Vollendung.

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

**Build-Skizze (nach Abschluss der v1.7.1-Build-Sequenz):**

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
### Klangwelt-Umsetzung

Übergabepunkt für die Klangwelt-Methodik-Umsetzung. Bei Sessionbeginn zuerst lesen — der Block zeigt, an welchem Schritt der Plan steht und welche Doku-Quellen nachzuschlagen sind.

#### Pflichtlektüre vor Wiedereinstieg

- **`Klangwelt/MCK_Methodik.md`** — alle Designentscheidungen (9 Sektionen, 539 Zeilen). Insbesondere Sektion 4 (Aktions-Klassifikation, 16-Aktionen-Liste mit Welt-Quellen), Sektion 6 (8-Slot-Schablone und Hybrid-Strategie), Sektion 7 (Iterations-Methodik mit Experimenten 1–7).
- **`Klangwelt/MCK_Bewertung.xlsx`** — Inventur + 5 Welt-Sheets (Morgenwald, Salon, Cityflow, Horizont, System) mit 16 Sound-Slots pro Welt. Bewertungsskala 0–5 in Legende.
- **`Klangwelt/MCK_Inventur.md`** Sektion 5 — Sound-Verdienst-Beschluss mit kanonischer Trigger-Stellen-Tabelle.
- **`Klangwelt/MCK_Chartas.md`** — Welt-Identität und Kernmotiv-Vorschläge pro Welt.
- **`Klangwelt/MCK_Manifest.md`** — Designprinzipien (Causality, Harmony, Utility, Sound-Weight) und Negativliste.

#### Zentrale Beschlüsse aus Session 06.05.2026

Punkte, die nicht alle explizit in der Methodik stehen, aber den Wiedereinstieg prägen:

- **Welt-Reihenfolge bei der Produktion:** Salon → Morgenwald → Cityflow → Horizont → System (Begründung: Klavier-Vertrautheit kalibriert das Welt-Vokabular am leichtesten; System zuletzt wegen eigener Sound-Design-Schule)
- **Cluster-Reihenfolge innerhalb einer Welt:** Aufgabenbezug → Tagesbezug → Rest (Begründung: kurze Aufgaben-Sounds sind der Härtetest, Tagesgesten skalieren das etablierte Vokabular nach oben, Klasse-C-Sounds sind Synthese)
- **Sound-Strategie:** Hybrid — Kernmotiv pro Welt aus Charta als Stilanker (nicht reproduzierbare Komposition), 8-Slot-Schablone pro Aktion variiert das Motiv über Slots 3–5
- **Funktion-Slot-Praxis:** Beispiele (z. B. *„like a quiet acknowledgment"*) werden am erzeugten Klang justiert, nicht abstrakt vor der Generierung
- **ElevenLabs-Korrekturfaktor:** 10–20 % länger als Soll-Dauer im Prompt ansagen (Tool generiert kürzer)
- **Bewertungs-Schwellen:** ≥3 für Bundle, ≥4 als Welt-„fertig", 5 = unverrückbar
- **Kollaborations-Workflow:** Claude generiert Prompts, Chris generiert in ElevenLabs (3–4 Varianten pro Prompt), gemeinsame Auswertung mit Eintrag in `MCK_Bewertung.xlsx`

#### Phase 1 — Testphase (Methodik kalibrieren)

Ziel: Empirisch belegte Bänder für Sound-Längen, justierte Prompt-Praxis, kalibrierte ElevenLabs-Erwartung. Vor Phase 2 abgeschlossen.

- [ ] **1a — Längen-Verifikation** (Experiment 1+2 aus Methodik §7) — Welt Salon, Aktion `teilaufgabeErledigt`. Drei Längen-Varianten (0.5 / 0.7 / 1.0 s) — Slots 1–6 und 8 identisch, nur Slot 7 (Dauer) variiert. Auswertung: welche Länge sitzt am rechtesten? Wie viel Stille-Padding hängt ElevenLabs typisch dran? Verschieben sich die Bänder in Methodik §3?
- [ ] **1b — Slot-Wirksamkeit + Prompt-Detailtiefe** (Experiment 3+4) — Resolution-Slot (5) explizit vs. weich. Detailtiefe minimal vs. mittel vs. üppig. Welten Salon (`hauptaufgabeKomplett`) und Morgenwald (`tagBegonnen`).
- [ ] **1c — Welt-Identitätsschärfe** (Experiment 5) — Identische Aktion (`teilaufgabeErledigt`) in Salon vs. Horizont. Welt-Charakter klar wahrnehmbar oder verwandt?
- [ ] **1d — System-Welt-Erstkontakt** (Experiment 7, vorläufig) — Erste System-Welt-Sounds gegen iOS-System-IDs hören. Robustheits-Anforderung der Charta: sharp transients, mid-frequency focus, definierter Hüll-Ende. Endgültiger Test in Phase 5b nach Code-Etappe.

#### Phase 2 — Produktion Klangwelt 1: Salon

Ziel: Salon vollständig generiert, bewertet, kalibriert. Methodik durch erste vollständige Welt geschärft, Erkenntnisse fließen in die folgenden Welten ein.

- [ ] **2a — Aufgabenbezug** — Slots 02, 03, 04, 07 (Klasse-A kategoriebezogen) plus Slots 08, 09, 10 (Klasse-B kategoriebezogen)
- [ ] **2b — Tagesbezug** — Slots 01, 05, 06 (Klasse-A Standard-Klangwelt)
- [ ] **2c — Rest** — Slots 11, 12, 13 (Klasse-B Standard-Klangwelt) plus Slots 14, 15, 16 (Klasse-C Synthese)
- [ ] **2d — Evaluation Salon** — alle 16 Slots ≥4 in `MCK_Bewertung.xlsx`. Reflektion: was hat sich bewährt, was muss justiert werden bevor Welt 2 startet? Methodik-Update falls nötig.

#### Phase 3 — Produktion der übrigen Welten

Pro Welt analog zu Phase 2: Aufgabenbezug → Tagesbezug → Rest → Evaluation.

- [ ] **3a — Morgenwald** (auditory icons; verifiziert dass die Methodik nicht nur für Earcons gilt)
- [ ] **3b — Cityflow** (Hybrid-Welt mit dokumentierter Schwäche: Tagesabschluss-Resolution im Lo-Fi-Vokabular schwer)
- [ ] **3c — Horizont** (synthetische Earcons; Methodik dann eingespielt)
- [ ] **3d — System** (eigene Sound-Design-Schule, Charta-Strategie C mit Rückfall B; komplexester Fall, daher zuletzt)

#### Phase 4 — Code-Änderungen (Audio-Session-Robustheit)

Voraussetzung: mindestens Phase 2 abgeschlossen — erste WAVs liegen vor, Robustheits-Tests sinnvoll. Vorgesehen als Build 10728 oder eigene Sequenz; bei Etappenplanung entscheiden, ob ein einziger Build oder mehrere.

- [ ] **4a — Audio-Session** — `.ambient` mit `.duckOthers`-Option umstellen (heute `.ambient` ohne Option); Volume-Resolution mit `isOtherAudioPlaying`-Check für Quiet-Stufe (siehe Methodik §5)
- [ ] **4b — Pre-Loading** — Klangwelt-WAVs bei erster Anforderung in `AVAudioPlayer`-Instanzen cachen; optional Pre-Warming-Pass beim App-Start für aktive Welten
- [ ] **4c — SoundActions-Konsolidierung 17→16** — entfernt: `hauptaufgabeErledigt`, `hauptaufgabeUebersprungen`, `aufgabeErstellt`, `aufgabeGeloescht`, `erledigteEntfernt`, `aufgabeZurueckgesetzt`, `exportImport`, `uiAktion`. Neu: `.aktionRegistriert`, `.aktionAbgeschlossen`, `.warnungVorAktion`, `.updateVerfuegbar`, `.syncCompleted`, `.syncFailed`. Trigger-Stellen aus Inventur §5 anwenden.
- [ ] **4d — Variable umbenennen** — `dayBookendSoundWorld` → `defaultSoundWorld`, UI-Label „Standard-Klangwelt", Hilfetext angepasst. Plus: Bullet-Point in Doku-Pflege „Bekannte Fallstricke" (Z. 225) auf neuen Stand aktualisieren (`.ambient` mit `.duckOthers`).
- [ ] **4e — Fallback-Logik abbauen** — System-Welt bekommt eigene WAVs, kein System-Sound-ID-Fallback mehr nötig
- [ ] **4f — Player-Pool oder bewusster Abbruch** — Methodik-Empfehlung: bewusster Abbruch (Single-Instance bleibt). Falls Tests in Phase 5 harte Cuts als unangenehm zeigen: gefadeter Abbruch mit ~50 ms Volume-Fade

#### Phase 5 — Test und Evaluation am Gerät

Voraussetzung: Phase 3 (alle Welten produziert) und Phase 4 (Code mit neuer Architektur) abgeschlossen.

- [ ] **5a — Robustheits-Test** (Experiment 6) — Klangwelt-Sounds parallel zu laufender Apple-Music am echten iPhone. Kommt der Sound durch? Ist das Ducking angemessen? Volume-Quiet-Eskalation auf Normal bei Hintergrundmusik wahrnehmbar?
- [ ] **5b — System-Welt-Vergleich finaler Pass** (Experiment 7) — System-WAVs vs. iOS-System-IDs A/B am Gerät. Erfüllen unsere System-WAVs die Robustheits-Anforderung der Charta?
- [ ] **5c — Bewertungs-Eintrag finalisieren** — alle 80 Slots in `MCK_Bewertung.xlsx` ≥4. Welten, die nicht erreicht werden, in eine Re-Generierungs-Welle planen.
- [ ] **5d — TestFlight-Phase** — Build mit neuer Klangwelt im TestFlight, eigene Test-Phase (1–2 Wochen) mit täglichem Gebrauch der App
- [ ] **5e — App-Store-Submission** — wenn alle Tests grün und keine F-Befunde aus dem TestFlight aufgetaucht sind

#### Erledigte Klangwelt-Etappen (Historie)

- [x] **WAV-Hygiene 04.05.2026** — Cityflow-Dateien mit Leerzeichen umbenannt, tote `*uiAktion.wav`-Dateien entfernt, Subordner-Casing konsistent. App-Repo Commit `59aac74`. Doku in `Klangwelt/MCK_Dateischema.md`.
- [x] **Sound-Verdienst-Beschluss 06.05.2026** — Inventur Sektion 5 mit kanonischer Trigger-Stellen-Tabelle, sechs neuen SoundActions, Audio-Session-Konfiguration `.ambient + .duckOthers`. Doku-Repo Commit `d09e20c`.
- [x] **Klangwelt-Methodik vollständig befüllt 06.05.2026** — `MCK_Methodik.md` aus Skeleton (243 B) zu Vollfassung (539 Zeilen, 9 Sektionen). Doku-Repo Commit `2719f79`.
- [x] **MCK_Bewertung.xlsx restrukturiert 06.05.2026** — auf 16-Aktionen-Architektur, 7 Sheets (Inventur + 5 Welten + Legende), Dateinamen-Schema `<welt-2b>_<klang-2b><slot-2-stell>.wav`. Doku-Repo Commit `27d90db`.

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

**Aktueller Stand (04.05.2026):**
- **v1.7.0 (Build 10726) seit 04.05.2026 im App Store live.** Builds 10713–10726 alle abgeschlossen, getaggt, gepusht. Letzter Code-Build vor Submission war 10726 (WhatsNewView).
- **F12 + F13 am 04.05.2026 verifiziert und geschlossen** — beide nicht reproduzierbar, vermutlich implizit gefixt durch den `setStatus(_:manually:)`-Helper aus 10711 und die Subtask-Operations-Konsolidierung aus 10716/10717.
- **Build 10724 (MusicPlayerView-Konsolidierung) ist Bestandteil von v1.7.0** — die kurzzeitige Verschiebung nach v1.7.1 in der Doku am 04.05.2026 vormittags war Irrtum, am Nachmittag korrigiert.
- v1.7.1-Build-Sequenz: **10727 F15** (Lautstärke-Differenzierung, erledigt 04.05.2026), **10728 Sound-Kaskaden** (B-S2, inhaltlich aufgegangen im Klangwelt-Phasen-Plan Phase 4f).

**Nächste Etappe(n):**
1. **Klangwelt-Methodik-Umsetzung** — Phasen-Plan in Sektion „Klangwelt-Umsetzung". Aktuell: Phase 1 (Testphase mit Längen-Verifikation, Slot-Wirksamkeit, Welt-Identitätsschärfe, System-Welt-Erstkontakt). Code-Etappe (Phase 4) erst nach Phase 2 (Salon-Produktion).

**v1.7.1-Backlog ohne feste Buildnummer:**
- F4 (markOpen lässt Folge-Instanz bestehen) — Klärung gemeinsam mit B1, weil verwandte Frage „was passiert mit erledigten Instanzen wenn man sie reaktiviert"
- F5 (kurzlebige Duplikate in Abgeschlossen-Sektion) — reine Verifikations-Etappe, vermutlich durch 10715 (TaskScheduler `@MainActor`) bereits mit-gelöst
- F6 (`unsafeForcedSync`-Console-Warning) — Diagnose-Etappe, kein Blocker
- F7 (`doesNotExpire`-Konzept-Review) — Konzept-Punkt im Plan-vs-Realität-Block
- F9 (Inline-Expand in Heute-Liste schwer auffindbar) — UX-Frage
- F14 (`AddTaskView`-Fokus-Latenz) — Diagnose-Etappe, Lade-Profil prüfen
- B1 (Historie als echtes Protokoll) — größerer Konzept- und Code-Block, eigene Strategie-Session sinnvoll vor Umsetzung

**Reviews & Design:**
- TemplatePaletteReview, Hauptaufgaben-Design (KlangweltReview siehe eigene Sektion „Klangwelt-Umsetzung" mit Phasen-Plan)
- App-weite Farbpalette-Konsistenz (Vermerk aus 10722 — Status-Punkt vs. Fortschrittsring)

**Onboarding-Verfeinerung (v1.7.x):**
- Default-Kategorien + Mustertemplate „MyCadenza einrichten" — siehe Konzept-Dokument `MyCadenza_FeatureKonzepte_v1_7_x.md`. Nach Abschluss der v1.7.1-Build-Sequenz.

**Strategische Themen für eigene Strategie-Sessions:**
- Strict Concurrency auf "Complete" — eigene Etappe wegen erwarteter Welle, nach v1.7.1
- Plural-Variations für Format-Strings (Vermerk aus 10721) — relevant bei Sprach-Erweiterung über DE/EN hinaus
- Pricing, Distribution, Vision — wenn das Thema reif ist, nach Beobachtungsphase v1.7.0

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
- `_eingefroren/MyCadenza_CodeReview_v1_7_0.md` – Detailliste aller Code-Review-Findings, Session-Log, Umsetzungsplan
- `Cadenza_Projektstruktur.md` – Projektstruktur & Bekannte Fallstricke
- `MyCadenza_Funktionsuebersicht.md` – App-Funktionsweise
- `_eingefroren/MyCadenza_Zielarchitektur.md` – Strategischer Bezugsrahmen
- `MyCadenza_FeatureKonzepte_v1_7_x.md` – Detailkonzepte für Onboarding-Verfeinerung (Default-Kategorien, Mustertemplate)
