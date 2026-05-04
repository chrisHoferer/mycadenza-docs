# MyCadenza – Code Review v1.7.0 (abgeschlossen)

> **Status: Abgeschlossen mit v1.7.0 vom 03.05.2026** (Apple-Einreichung). Dieses Review wurde während der v1.7.0-Welle (Builds 10707–10722) zur strukturierten Befund-Verfolgung geführt. Mit Auslieferung von v1.7.0 ist die Befund-Sammlung abgeschlossen.
>
> **Offene Befunde** aus diesem Review sind in [`MyCadenza_TodoList_v1_7.md`](MyCadenza_TodoList_v1_7.md) (Block B v1.7.1) und in [`MyCadenza_Roadmap_v1_7_1_bis_v2_x.md`](MyCadenza_Roadmap_v1_7_1_bis_v2_x.md) weitergetragen.
>
> Dieses Dokument bleibt als **Befund-ID-Referenz** im Repo (für Commit-Messages, die auf `A-S1`, `B-M2`, `C-S37` etc. verweisen), wird aber nicht mehr fortgeschrieben.
>
> Die ursprünglichen Stand-Angaben unten bleiben als historischer Beleg unverändert.

---

> Lebendiges Review-Dokument für den strukturierten Code-Review-Prozess.
> Stand: 25.04.2026 · v1.6.1 im App Store · Build 10716 gepusht · TaskScheduler `@MainActor` (10715) + SubTask-Operations konsolidiert (10716)
> Methodik: Alle Befunde sammeln → Gesamtbild → strategische Einordnung → priorisieren → umsetzen

> **Begleitdokument:** `MyCadenza_Zielarchitektur.md` — Phasenplan, auf den sich dieses Review bezieht.

---

## 0. Status: bereits erledigte Altlasten

Aus der CloudKit-Debugging-Session (Memory-Stand vor diesem Review) waren fünf Punkte offen. Beim Durchsehen für diesen Review zeigt sich:

| # | Punkt | Status |
|---|---|---|
| 1 | `@Relationship(inverse:)` explizit auf MainTask↔SubTask, TaskTemplate↔TemplateSubTask, TaskCategory↔beide | ✅ erledigt |
| 2 | `cleanupDuplicates()` löschte bei zwei offenen Duplikaten das neuere Objekt | ✅ erledigt (neue `preservationScore`-Heuristik) |
| 3 | `NSPersistentStoreRemoteChange` ruft `processPendingChanges()` (no-op bei Remote) | ✅ erledigt (`scheduleRemoteChangeProcessing` mit 1.5 s-Coalescing) |
| 4 | `processAllTasks()` feuert exzessiv bei jedem `scenePhase == .active` | ✅ erledigt (30 s-Debounce + `force:`-Escape) |
| 5 | JSON-Export enthält `soundWorldRaw` der Kategorien nicht | ✅ erledigt (CategoryExport.soundWorldRaw) |

→ Damit startet der Review auf sauberer Basis.

---

## 1. Methodik

Drei Kategorien für jeden Befund:

- 🔴 **muss** – Bug oder Funktionslücke mit konkreter Auswirkung auf Nutzer/Daten/CloudKit.
- 🟡 **sollte** – Keine akute Auswirkung, aber erhöhtes Risiko oder Wartbarkeitsproblem.
- 🔵 **Anmerkung** – Kosmetik, Mikro-Optimierung, gut zu wissen. Kein Handlungsdruck.

Jeder Befund hat eine Kennung (z.B. `A-M1`, `C-S23`). Zusätzlich ein Strategie-Tag:

- 🟢 **unabhängig** — jetzt final lösbar

Mit der Zielarchitektur-Klarstellung vom 18.04.2026 (MyCadenza bleibt Solo-App, keine Multi-User-Zukunft) sind **alle Befunde 🟢**. Es gibt nichts mehr, das auf strategische Entscheidungen warten müsste.

---

## 2. Teil A – Datenmodell + Persistenz

Geprüft: `Models.swift`, `TaskCategory.swift`, `TaskTemplate.swift`, `CadenzaApp.swift`, `CadenzaExportImport.swift`, `DateMigrator.swift`

### 🔴 muss

#### A-M1 · Batch-Replace-Import ohne Zwischen-Save · 🟢

**Datei:** `CadenzaExportImport.swift`, Zeilen 261–337
**Problem:** Bei `mode == .replace` werden alle Objekte in einer einzigen Transaktion gelöscht und neu inserted. CloudKit-Pain-Point: Deletes + Mass-Insert in einem `save()` wird nicht zuverlässig gepusht.
**Lösung:** Drei Zwischen-Saves (nach Delete, nach Kategorien, nach Aufgaben).
**Umsetzung:** ✅ erledigt in Build 10706

### 🟡 sollte

#### A-S1 · `status.set` hat versteckten Side-Effect · 🟢

**Datei:** `Models.swift` (vor 10711)
**Problem:** Der Setter von `status` setzt implizit `isManualStatus = true`. Inkonsistente API.
**Befund aus Teil C:** An **9 Stellen** im View-Layer wurde dieser Setter bewusst umgangen durch das Raw-Muster `task.statusRaw = TaskStatus.done.rawValue; task.isManualStatus = true`. Der Side-Effect war faktisch nicht mehr im Einsatz.
**Lösung:** Model-Methoden `markDone(manually:)`, `markSkipped(manually:)`, `markOpen(manually:)` eingeführt, die `statusRaw` + Flag + `lastExecutedDate` + SubTasks-Propagation an einer Stelle kombinieren. Setter-Side-Effect entfernt. Name `isManualStatus` bewusst beibehalten (Option C der Refactor-Diskussion — Umbenennung zu `isStatusLocked` wäre eine persistent-Property-Migration mit CloudKit-Schema-Risiko gewesen).
**Umsetzung:** ✅ erledigt in Build 10711

#### A-S2 · Code-Duplikation in `CadenzaApp` · 🟢

**Datei:** `CadenzaApp.swift`, Zeilen 78–88
**Problem:** `runStartupProcessing()` und `runForegroundProcessing()` sind zeilenidentisch.
**Umsetzung:** ✅ erledigt in Build 10702

#### A-S3 · Silent Fallbacks beim Import · 🟢

**Datei:** `CadenzaExportImport.swift`
**Problem:** `RepeatInterval(rawValue:) ?? .daily` und `TaskStatus(rawValue:) ?? .open` schlucken unbekannte Werte ohne Log.
**Umsetzung:** ✅ erledigt in Build 10703

### 🔵 Anmerkung

#### A-A1 · `applyExpiry` setzt `isManualStatus = true` · 🟢
Zusammenhängend mit A-S1. In Build 10711 gemeinsam gelöst: `applyExpiry()` ruft jetzt `markSkipped(manually: true)`.
**Umsetzung:** ✅ erledigt in Build 10711

#### A-A2 · `@Relationship` ohne expliziten `deleteRule` · 🟢
`TaskCategory.swift`. Default ist `.nullify`, was hier gewollt ist. Explizit schreiben.
**Umsetzung:** ✅ erledigt in Build 10702

#### A-A3 · `doesNotExpire` auf nicht-`.once`-Aufgaben · 🟢
Model-seitiger Invariant fehlt. Solange die UI das verhindert, unkritisch.

---

## 3. Teil B – Scheduler + Services

Geprüft: `TaskScheduler.swift`, `SoundManager.swift`, `DemoDataProvider.swift`

### 🔴 muss

#### B-M1 · Background Task mit falschem ModelContainer · 🟢

**Datei:** `TaskScheduler.swift`, Zeilen 373–383
**Problem:** `handleBackgroundTask(task:)` erstellt einen eigenen `ModelContainer(for: MainTask.self, SubTask.self)` — unvollständiges Schema, ohne CloudKit-Konfiguration.
**Lösung:** Static Shared Container (`PersistenceController.shared`) vom Handler wiederverwendet.
**Umsetzung:** ✅ erledigt in Build 10705

#### B-M2 · Kategorien-Duplikate bei parallelem Import und CloudKit-Sync · 🟢 · neu entdeckt 18.04.2026

**Szenario:** App wird neu installiert. Beim ersten Öffnen startet CloudKit automatisch den Download. Wenn der Nutzer parallel "Daten importieren → Ersetzen" startet, kollidieren die beiden Datenquellen: doppelte Kategorien.

**Lösungsrichtung (von Chris gewählt):** Import blockieren, solange CloudKit-Sync aktiv ist. Klare Nutzerführung: "iCloud-Sync aktiv, bitte später nochmal probieren."

**Designfragen für die Umsetzungs-Etappe:**
- Wie erkennt die App zuverlässig, dass CloudKit-Sync aktiv ist? (NSPersistentStoreRemoteChange als Aktivitätsindikator?)
- Wann gilt Sync als "nicht mehr aktiv"? (X Sekunden ohne neue Notification?)
- Gilt die Blockade auch für "Zusammenführen"-Import, oder nur für "Ersetzen"?
- UI-Text in DE und EN

**Umsetzung:** ⬜ offen, geplant für Build 10721 (verschoben von 10717, weil 10715/10716 zwischengeschaltet wurden)

### 🟡 sollte

#### B-S1 · Race Conditions auf statischem State · 🟢

**Datei:** `TaskScheduler.swift`
**Problem:** `isProcessing`, `lastProcessedDate`, `pendingRemoteChangeWorkItem` sind `static var` ohne Synchronisation.
**Lösung:** `@MainActor` auf gesamte Klasse `TaskScheduler` (Variante B aus der Refactor-Diskussion). BG-Closure hopt mit `Task { @MainActor in ... }`. `DispatchWorkItem` → `Task` migriert (`pendingRemoteChangeTask`).
**Beifang:** Behebt zugleich den TestFlight-BG-Task-Crash (SwiftData-Thread-Violation auf Worker-Thread). Der B-M1-Fix (10705) hatte das Race-Condition-Problem freigelegt.
**Anmerkung aus Teil C:** MusicManager hat einen ähnlichen `static`-State, aber kein `static var` — `@MainActor` für MusicManager als spätere Etappe.
**Umsetzung:** ✅ erledigt in Build 10715

#### B-S2 · `audioPlayer` als einzelne Instanzvariable · 🟢

**Datei:** `SoundManager.swift`, Zeile 208
**Problem:** Jeder `play`-Call überschreibt den vorherigen.
**Abhängigkeit:** Produkt-Entscheidung — soll die Kaskade erlaubt werden (Player-Pool) oder ist der Abbruch gewollt? Chris entscheidet nach Klangwelten-Review.
**Umsetzung:** ⬜ offen (wartet auf Chris-Entscheidung), geplant für Build 10722

#### B-S3 · `DemoDataProvider.deactivate()` dupliziert Importer-Logik · 🟢

**Datei:** `DemoDataProvider.swift`, Zeilen 78–185
**Umsetzung:** ✅ erledigt in Build 10704

#### B-S4 · `deleteAllData()` doppelt implementiert · 🟢

**Dateien:** `CadenzaExportImport.swift` + `DemoDataProvider.swift`
**Umsetzung:** ✅ erledigt in Build 10704 (zusammen mit B-S3)

### 🔵 Anmerkung

#### B-A1 · Reminder-Identifier mit `timeIntervalSince1970` · 🟢
`TaskScheduler.swift`, Zeile 320. Fragil bei Zeitverschiebungen. UUID wäre sauberer. `repeats: false`, keine akute Gefahr.

#### B-A2 · `AVAudioPlayer` neu pro Call · 🟢
`SoundManager.swift`, Zeilen 376–398. Bei 0.5-s-Sounds vernachlässigbar.

#### B-A3 · Haptik trotz `isEnabled = false` · 🟢
`SoundManager.swift`, Zeilen 343–347. Explizit so gewollt. ✓

#### B-A4 · Kein expliziter `cancel` vor `scheduleReminder` · 🟢
`TaskScheduler.swift`. Harmlos, da `add(request:)` überschreibt.

---

## 4. Teil C – Views

Geprüft: 17 Dateien, ~6.350 Zeilen Swift-Code + Info.plist.

### 🔴 muss

#### C-M1 · `String(localized:)`-API-Missbrauch in WhatsNewView · 🟢

**Datei:** `OnboardingView.swift`, Zeile ~172
**Fix:** `Text(String(format: String(localized: "Version %@"), versionText))` oder moderne API.
**Umsetzung:** ✅ erledigt in Build 10710

#### C-M2 · HistoryView hardcoded Datumsformat · 🟢

**Datei:** `HistoryView.swift`, Zeilen 265–283
**Fix:** `formatter.setLocalizedDateFormatFromTemplate("EEEEdMMMM")` bzw. `"dMMM"`.
**Umsetzung:** ✅ erledigt in Build 10710

#### C-M3 · App-Name-Tippfehler in DailySummaryScheduler · 🟢

**Datei:** `SettingsView.swift`, Zeile 865 (vs. Zeile 819)
- Zeile 865: `"Dein Tag mit My Cadenza"` (mit Leerzeichen – falsch!)
- Zeile 819: `"Dein Tag mit MyCadenza"` (korrekt)

Aktuell unschädlich, weil `scheduleWithContext` toter Code ist (siehe C-S16).
**Umsetzung:** ⬜ offen, gebündelt mit Tote-Code-Etappe (10714)

#### C-M4 · Validity-Duration-Anzeige schneidet Halbstunden ab · 🟢

**5 Stellen:** CreateTaskFromTemplateView, TemplateView (2x), EditorView (2x)
**Lösung:** Gemeinsame Helper-Funktion `formatDuration(_:)` in `FormattingHelpers.swift`.
**Umsetzung:** ✅ erledigt (FormattingHelpers.swift existiert im Codebase)

### 🟡 sollte

Die vollständige Befundliste (49 Einträge in Kategorien Konsolidierung/DRY, Robustheit/Korrektheit, Toter Code/Cleanup, Localization-Cluster) bleibt wie in der Vorversion. Hervorgehoben:

**C-S37 · `cleanupCompletedSubTasks` / `resetAllSubTasks` dreifach dupliziert** — EditorView.EditTaskView + TodayView.TodayTaskRowView + TodayView.SubTaskSheetView.

**Nebenbefund aus 10711-Testing:** `cleanupCompletedSubTasks` in TodayView (Z. 1310) vergisst `isManualStatus = false` vor `syncStatus()` — in EditorView (Z. 939) ist es drin. Diese Inkonsistenz wird mit C-S37 in Build 10713 automatisch korrigiert. **Gleichzeitig** wird der in 10711 bewusst als Raw belassene Cleanup-Unlock-Ausreißer (EditorView Z. 939) mit aufgeräumt.

**C-S41 · Menu-Button-Blocks (~30 Zeilen je Button)** — mit A-S1 in 10711 durch die neue Model-Methoden-API bereits deutlich kürzer. Formaler Abschluss mit C-S39.

### 🔵 Anmerkung

Siehe Vollversion in Git-History ab Tag `v1.7.0-b10702`. Keine akuten Handlungen nötig.

### Bezug zu A-S1 / A-A1 — Call-Sites-Migration (10711)

**Tatsächliche Migration in Build 10711:**

**EditorView.swift (2 migriert, 1 bewusst Raw):**
- Z. 843 (DEBUG-Button) → `markDone()` ✅
- Z. 947 (Reset) → `markOpen(manually: false)` ✅
- Z. 939 (Cleanup-Unlock) — bleibt Raw, wird mit C-S37 in 10713 aufgeräumt

**TodayView.swift (6 migriert):**
- Z. 1182–1184 (Menu "Als offen") → `markOpen()` ✅
- Z. 1190–1193 (Menu "Als erledigt") → `markDone()` ✅
- Z. 1209–1212 (Menu "Als übersprungen") → `markSkipped()` ✅
- Z. 1327–1329 (Reset TodayTaskRow) → `markOpen(manually: false)` ✅
- Z. 1344–1346 (Snapshot) → `markDone()` ✅
- Z. 1643–1645 (Reset SubTaskSheet) → `markOpen(manually: false)` ✅

**Summe:** 8 von 9 Raw-Muster-Stellen migriert. Eine bewusst als Ausreißer gelassen.

---

## 5. Gesamtbild

**Quantitativ:**
- Teil A: 7 Befunde (1 muss · 3 sollte · 3 Anmerkung)
- Teil B: 10 Befunde (2 muss · 4 sollte · 4 Anmerkung)
- Teil C: 91 Befunde (4 muss · 49 sollte · 38 Anmerkung) — inkl. Block 11 (SoundManager, PersistenceController, Info.plist)
- **Summe: 108 Befunde**
- **Alle 🟢 — lösbar in Phase 0**

**Fortschritt Stand 25.04.2026:**
- ✅ **15 Befunde erledigt:** A-M1, A-S1, A-S2, A-S3, A-A1, A-A2, B-M1, B-S1, B-S3, B-S4, C-M1, C-M2, C-M4, C-S37 (+ 3 Sub-Bugfixes als Beifang von 10716)
- ⬜ **93 Befunde offen**, davon 1 muss (B-M2), viele sollte, viele Anmerkung — verteilt auf die Etappen 10717–10724+
- Plus 11 UX-Findings (F1, F3-F11) für v1.7.1 in der TodoList

---

## 6. Umsetzungsplan mit Etappen-Buildnummern

Die Arbeit wird in Etappen gegliedert. Jede Etappe bekommt eine aufsteigende Buildnummer und einen Git-Tag, damit Rollback einer einzelnen Etappe möglich bleibt.

> **Hinweis zur Realität der Buildnummern 10707–10711:** Der ursprüngliche Plan ordnete 10707 den C-M-Hotfixes, 10708 dem Testmodus-Framework, 10709/10710/10711 dem 3-stufigen A-S1-Refactor zu. Tatsächlich wurden 10707/10708 in TestFlight gebracht, 10709 bekam zwei akute Bugfixes, 10710 die beiden C-M-Hotfixes, und 10711 wurde die volle A-S1-Etappe (Methoden bauen + Call-Sites migrieren + Setter-Side-Effect entfernen, alles in einem). Kein Nachteil — der Refactor ist komplett durch, Commit + Tag gesetzt.

### Erledigt

| Build | Etappe | Befunde | Status |
|---|---|---|---|
| **10701** | Baseline | — | ✅ |
| **10702** | DRY + Kosmetik | A-S2, A-A2 | ✅ |
| **10703** | Silent Fallbacks | A-S3 | ✅ |
| **10704** | Import-Helper konsolidieren | B-S3, B-S4 | ✅ |
| **10705** | BG-Task-Container-Fix | B-M1 | ✅ |
| **10706** | Batch-Replace-Saves | A-M1 | ✅ |
| **10707** | TestFlight-Hotfix-Welle | Interne Korrekturen | ✅ TestFlight |
| **10708** | TestFlight-Hotfix-Welle | Interne Korrekturen | ✅ TestFlight |
| **10709** | Bugfixes | Trash-Icon · SubTaskEditor-Linebreaks | ✅ |
| **10710** | C-M-Hotfixes | C-M1 (WhatsNewView) · C-M2 (HistoryView-Datumsformat) | ✅ |
| **10711** | **A-S1 komplett (a+b+c)** | A-S1, A-A1 · Model-API `markDone/markSkipped/markOpen` · Setter-Side-Effect entfernt · 8/9 Call-Sites migriert | ✅ |
| **10712a** | B2 Default-Startzeit | Nächste halbe Stunde mit Minuten-Granularität, `Date+MyCadenza.swift` neu | ✅ |
| **10712b** | B3 Vorab-erledigte Zukunftsaufgaben | Abgeschlossen-Sektion erweitert, `syncStatus()` idempotent, Fortschrittsring geschützt | ✅ |
| **10712c** | F2 markOpen reicht durch | `.open`-Case in `applyStatusToSubTasks()` symmetrisch zu markDone/markSkipped, entsperrt grundsätzlich | ✅ |
| **10715** | **TaskScheduler @MainActor** | B-S1 + TestFlight-BG-Crash-Fix | ✅ TestFlight |
| **10716** | **SubTask-Ops konsolidiert** | C-S37 + 3 Sub-Bugfixes (Sound im SubTaskSheetView, isManualStatus konsistent, Cleanup-Unlock-Ausreißer) | ✅ |

### Geplant

| Build | Etappe | Befunde | Aufwand |
|---|---|---|---|
| **10717** | **DRY-Konsolidierung II (Subtask-Toggle-UI)** | C-S38 + C-S39. Beifang: F10 (EditTaskView eigene Toggle-Implementierung ohne Sound). Wenn EditTaskView auf `InlineSubTaskRow` umgebaut wird, fallen Sound-Lücke und Code-Duplikation zusammen weg. | mittel |
| **10718** | **F11-Diagnose** (EditTaskView Cleanup-Sound) | F11. Voraussetzung: Begriffs-Glossar für die View-Hierarchie. Präzises Schritt-für-Schritt-Verfahren. | klein |
| **10719** | **Tote-Code-Etappe** | C-S5, C-S16, C-S18, C-S22, C-S29 (swipeActions-Duplikate, tote Funktionen). C-M3 nebenbei. Beifang: F1, F3, F8 falls umfangsverträglich. | klein-mittel |
| **10720** | **Localization-Cleanup** | Dynamische `String(localized: "... \(var) ...")`-Keys auf `String(format:)` umstellen. Hardcoded deutsche Strings in Pickern. | mittel |
| **10721** | **Import-Sperre bei CloudKit-Sync** | B-M2. **Letzter v1.7.0-Release-Blocker.** | mittel |
| **10722** | **Sound-Kaskaden** | B-S2 (abhängig von Chris' Klangwelten-Entscheidung) | klein |
| **10723** | **MusicPlayerView Konsolidierung** | C-S44, C-S45, C-S46 (Placeholder + AsyncImage in gemeinsame Komponenten). C-S43 (fetchTracks generisch). | klein-mittel |
| **10724** | **SoundManager preview-API** | C-S42 (SoundtestView delegiert an SoundManager.preview). C-A45 (tagKomplett-Doppelton in SoundManager). | klein |
| **10725** | **Form-Refactor** (optional, risikobehaftet) | C-A21 + C-S25 + C-S34 (drei AddX/EditX-Paare als FormFields-Komponenten). | groß |
| **10726+** | **Ausstehende Anmerkungen** | Restliche 🔵-Befunde nach Bedarf. | klein bis mittel |

### DRY-Etappe I (offen)

`aggregatedSubTaskCounts` + `groupedByCategory` als Extensions. Reduziert 4 + 5 Duplikate. Klein genug für Mini-Etappe (10717a oder mit 10719 zusammen).

### Rhythmus pro Etappe

1. `git status` prüfen — working tree clean
2. Code-Änderung
3. Buildnummer im Xcode erhöhen
4. Clean Build (⌘⇧K + ⌘B)
5. Testmodus aktivieren, etappenspezifischen Testdatensatz laden, Testpfad durchspielen
6. Git-Commit mit Build-Nummer in der Message
7. Git-Tag setzen: `v1.7.0-b10712`, `v1.7.0-b10713` etc.
8. Push mit `--follow-tags`
9. Nächste Etappe erst starten, wenn die aktuelle verifiziert ist

### Rollback-Strategie

Wenn eine Etappe Probleme bereitet: `git reset --hard v1.7.0-bXXXXX` auf den vorherigen Tag, Buildnummer im Xcode zurücksetzen. Damit ist der vorherige stabile Stand sofort wiederhergestellt.

---

## 7. Session-Log

| Datum | Build | Befund(e) | Lösung | Verifikation |
|---|---|---|---|---|
| 2026-04-18 | **10702** | A-S2, A-A2 | `runProcessing()` einheitlich, explizite `deleteRule: .nullify` | Simulator iPhone 17 Pro: App startet, Präsentationsmodus aktivieren → Tagesübersicht zeigt Daten, Hintergrund/Vordergrund-Wechsel ohne Probleme. Git-Tag `v1.7.0-b10702` gesetzt. |
| 2026-04-18 | **10703** | A-S3 | Private Helper `decodeRepeatInterval`/`decodeTaskStatus` mit Logging bei unbekannten Rawvalues | Simulator iPhone 17 Pro: Build sauber, App startet, Deaktivieren des Präsentationsmodus (durchläuft Importer für Echtdaten-Restore) ohne Probleme. Git-Tag `v1.7.0-b10703` gesetzt. |
| 2026-04-18 | **10704** | B-S3, B-S4 | `deactivate()` im `DemoDataProvider` delegiert an `CadenzaDataImporter.importData(from:Data,into:mode:)`. Zentrale `deleteAllData(context:)` als `static func` im Importer, Duplikat im Provider entfernt. | Simulator iPhone 17 Pro: Präsentationsmodus aktivieren, Test-Aufgabe angelegt, Präsentationsmodus deaktivieren — Echtdaten zurück, Test-Aufgabe weg. Git-Tag `v1.7.0-b10704` gesetzt. |
| 2026-04-18 | **10705** | B-M1 | Neue `PersistenceController.swift` mit Shared-Static-Container. Foreground + Background identisches Schema + CloudKit-Konfig. | Deployment iPhone: Build sauber, HeuteView zeigt Aufgaben, Hintergrund/Vordergrund-Wechsel ohne Probleme. Git-Tag `v1.7.0-b10705` gesetzt. |
| 2026-04-18 | **10706** | A-M1 | Drei Zwischen-Saves im Replace-Import: nach `deleteAllData`, nach Kategorien-Insert, finaler Save nach Tasks/Templates. | iPhone deployed. Export-Import-Zyklus erfolgreich. **Neuer Befund B-M2** entdeckt: parallel laufender CloudKit-Sync produziert Kategorien-Duplikate. Git-Tag `v1.7.0-b10706` gesetzt. |
| 2026-04-19 | — | Teil C Review | 14 Dateien geprüft (~5.800 Zeilen). 85 neue Befunde. Zentraler Befund: A-S1 ist Architekturarbeit. | Umsetzungsplan auf 10707–10722+ erweitert. |
| 2026-04-19 | — | Block 11 | SoundManager + PersistenceController + Info.plist. 6 neue Befunde. Bestätigt B-S2, identifiziert C-S47/C-S48/C-S49. | Review komplett. |
| ca. 2026-04-20/21 | **10707 · 10708** | TestFlight-Hotfix-Welle | Interne Hotfixes in TestFlight gebracht. | Tester-Rückmeldung erfolgt, weitere Fixes folgen in 10709/10710. |
| ca. 2026-04-21 | **10709** | Bug: Trash-Icon Teilaufgabenliste · Bug: SubTaskEditor-Sheet Zeilenumbruch statt Speichern | Bugfix-Kombination in einer Etappe. | Committed. |
| ca. 2026-04-22 | **10710** | C-M1 (WhatsNewView) · C-M2 (HistoryView-Datumsformat) | `String(format:)`-Pattern statt `defaultValue`-Missbrauch; `setLocalizedDateFormatFromTemplate` statt Hardcoded. | Committed. |
| **2026-04-24** | **10711** | **A-S1, A-A1** (volle 3-Etappen-Arbeit in einem Build) | Neue Model-API `markDone/markSkipped/markOpen(manually:)` auf `MainTask`. Gemeinsamer privater `setStatus(_:manually:)`-Helper. Setter-Side-Effect aus `status.set` entfernt. `applyExpiry()` nutzt jetzt `markSkipped`. 8 von 9 Raw-Muster-Call-Sites migriert; 1 Stelle (EditorView-Cleanup-Unlock Z. 939) bewusst Raw belassen, wird mit C-S37 in 10716 aufgeräumt. Name `isManualStatus` beibehalten (Option C — Umbenennung zu `isStatusLocked` wäre persistent-Property-Migration mit CloudKit-Schema-Risiko gewesen). | Clean Build grün. iPhone-Test: `markDone()` propagiert Status auf SubTasks korrekt; `markSkipped()` erstellt Folgeaufgabe korrekt; kein Crash. **Nebenbefunde aus Testing:** (a) TodayView.cleanupCompletedSubTasks Z. 1310 vergisst `isManualStatus = false` vor `syncStatus()` — wird in 10716 mit C-S37 gelöst. (b) Drei UX-Findungen "Plan vs. Realität" — separat dokumentiert in `MyCadenza_TodoList_v1_7.md` (v1.7.1-Block). Git-Tag `v1.7.0-b10711` gesetzt, gepusht. |
| 2026-04-24 | **10712a** | B2 | `Date+MyCadenza.swift` neu angelegt mit Helper für nächste halbe Stunde, Minuten-Granularität. Speicher-Fallback auf `Date()` wenn gewählte Zeit heute in der Vergangenheit liegt. | iPhone-Test, AddTask-Form öffnet mit nächster halber Stunde. Git-Tag `v1.7.0-b10712a` gesetzt. |
| 2026-04-24 | **10712b** | B3 | Vorab-erledigte Zukunftsaufgaben in Abgeschlossen, `syncStatus()` pflegt `lastExecutedDate` konsistent (idempotent), Fortschrittsring gegen Überlauf geschützt. | iPhone-Test, drei UX-Findungen entdeckt: F1, F3, F4, F5. Git-Tag `v1.7.0-b10712b` gesetzt. |
| 2026-04-24 | **10712c** | F2 | `.open`-Case in `applyStatusToSubTasks()` setzt jetzt alle `done`/`skipped`-Teilaufgaben auf `open` zurück, symmetrisch zu `markDone`/`markSkipped`. `markOpen` entsperrt grundsätzlich. | Test sauber. Git-Tag `v1.7.0-b10712c` gesetzt. |
| **2026-04-25** | **CloudKit-Schema-Check** | Production vs Development | Vergleich aller Record-Types und Felder im CloudKit Dashboard. Ergebnis: deckungsgleich (CD_MainTask 24, CD_SubTask 11, CD_TaskCategory 12, CD_TaskTemplate 17, CD_TemplateSubTask 10, Users 7). Schema-Mismatch als TestFlight-Crash-Ursache ausgeschlossen. | Diagnose verschoben auf Crash-Logs in Xcode Organizer. |
| **2026-04-25** | **Crash-Diagnose** | TestFlight-Bug | Xcode Organizer Crashes-Tab zeigte: Thread 4, Background-Task → handleBackgroundTask → processAllTasks → createNextIfNeeded → SwiftData-Crash. Klassischer SwiftData-Thread-Violation: BG-Task läuft auf Worker-Thread, mainContext ist MainActor-bound. Ursache identifiziert: B-M1-Fix (10705) hatte den geteilten Container eingeführt, dadurch wurde das B-S1-Race-Condition-Problem freigelegt. | Mit klarer Ursachen-Identifikation. |
| **2026-04-25** | **10715** | **B-S1, TestFlight-Crash** | Variante B aus der Refactor-Diskussion: `@MainActor` auf gesamte Klasse `TaskScheduler`. BG-Closure hopt mit `Task { @MainActor in ... }` auf MainActor, bevor `handleBackgroundTask` ausgeführt wird. `pendingRemoteChangeWorkItem: DispatchWorkItem?` → `pendingRemoteChangeTask: Task<Void, Never>?` migriert. `scheduleRemoteChangeProcessing` nutzt jetzt `Task { @MainActor in try? await Task.sleep(for: .seconds(1.5)) ... }`. | Drei-Stufen-Test: Simulator → echtes iPhone → TestFlight. Build kompiliert grün ohne CadenzaApp-Anpassung (Concurrency-Modus nicht "Complete"). iPhone-Test mit Simulate Background Fetch sauber, Console-Warning `unsafeForcedSync` als F6 notiert (kein Crash). TestFlight-Upload erfolgreich. Bei normaler iPhone-Nutzung kein Crash. Git-Tag `v1.7.0-b10715` gesetzt, gepusht. |
| **2026-04-25** | **10716** | **C-S37 + 3 Sub-Bugfixes** | Neue Datei `MainTask+SubTaskOperations.swift` mit `@MainActor`-Extension, `cleanupCompletedSubTasks(context:)` und `resetAllSubTasks(context:)` als Methoden auf `MainTask`. Drei vorher in `EditTaskView`/`TodayTaskRowView`/`SubTaskSheetView` duplizierte Methoden zusammengeführt. Drei Sub-Bugfixes als Beifang: (1) `isManualStatus = false` vor `syncStatus()` jetzt überall konsistent, (2) `playCompletedRemoved`-Sound im SubTaskSheetView jetzt vorhanden (vorher fehlte er komplett), (3) `playTaskReset`-Sound im SubTaskSheetView jetzt vorhanden. Cleanup-Unlock-Ausreißer aus 10711 mitgelöst. **Erkenntnis:** SoundManager ist bereits `@MainActor`-isoliert — daher Extension auch `@MainActor`. | Tests: Cleanup/Reset in EditTaskView ✅, TodayTaskRowView Inline ✅, SubTaskSheetView Cleanup + Reset (mit kategorisierter Aufgabe) ✅ — Sounds sind dort vorhanden, die vorher fehlten. Test 2 zu 10712a (Minuten-Granularität) nebenbei mitgelaufen ✅. **Zwei neue Befunde:** F10 (EditTaskView eigene Subtask-Toggle-Implementierung ohne Sound — wird in 10717 als Beifang von C-S38 gelöst), F11 (EditTaskView Cleanup/Reset-Sound möglicherweise nicht hörbar — Diagnose in 10718). Git-Tag `v1.7.0-b10716` gesetzt, gepusht. |

---

## 8. UX-Findings für v1.7.1

In den Test-Sessions zu 10711, 10712b/c, 10715 und 10716 sind insgesamt 11 UX-Findungen aufgetaucht, die mit der reinen Status-Semantik oder Code-Konsolidierung nichts zu tun haben, aber thematisch zusammenhängen — hauptsächlich rund um den gemeinsamen Nenner: **Die App denkt zu stark in `startTime` (Plan), nicht in `lastExecutedDate` (Realität).**

Vollständige Erfassung mit Status, Beschreibung und geplanter Etappe in `MyCadenza_TodoList_v1_7.md` Block "B) v1.7.1 — UX-Findings für v1.7.1".

**Übersicht:**

| # | Befund | Status | Geplante Etappe |
|---|---|---|---|
| F1 | Abgeschlossen-Sektion Darstellung schärfen | offen | 10719 als Beifang |
| F2 | `markOpen` reicht Status auf Teilaufgaben durch | ✅ erledigt | 10712c |
| F3 | SubTask-Sheet schließt sich nicht nach Löschen | offen | 10719 als Beifang |
| F4 | `markOpen` lässt Folge-Instanz bestehen | offen | gemeinsam mit B1 |
| F5 | Kurzlebige Duplikate in Abgeschlossen | mit-gelöst | 10715 (zu verifizieren) |
| F6 | `unsafeForcedSync`-Console-Warning | offen | eigene Diagnose-Etappe |
| F7 | `doesNotExpire`-Konzept-Review | offen | v1.7.1 Konzept-Block |
| F8 | "Alle zurücksetzen" auch sichtbar wenn nichts zu reseten | offen | 10719 als Beifang |
| F9 | Inline-Expand schwer auffindbar | offen | v1.7.1 |
| F10 | EditTaskView eigene Subtask-Toggle-Implementierung ohne Sound | offen | 10717 als Beifang von C-S38 |
| F11 | EditTaskView Cleanup-Sound nicht hörbar (zu verifizieren) | offen | 10718 mit Begriffs-Glossar |

**Bemerkenswert:** Build 10716 hat allein durch DRY-Konsolidierung **drei stille Bugs** korrigiert, die in der Code-Duplikation hängen geblieben waren — F11 hat diese Erkenntnis sogar noch weiter validiert, indem klar wurde, dass auch die nicht-konsolidierte EditTaskView (mit ihrer eigenen Subtask-Toggle-Implementierung) entsprechende Sound-Lücken hat. Argumentativ wertvoll für die Begründung der Konsolidierungsetappen 10717+.
