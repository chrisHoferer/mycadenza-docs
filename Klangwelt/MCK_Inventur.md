# Sound-Inventur MyCadenza

> **Stand:** 06.05.2026 · Basis: **v1.7.1, Build 10727** (`MARKETING_VERSION = 1.7.1`, `CURRENT_PROJECT_VERSION = 10727` aus `ChrisMeinTag.xcodeproj/project.pbxproj`) · Sektion 5 (Sound-Verdienst-Beschluss) am 06.05.2026 angelegt — Arbeitsgrundlage für MCK_Methodik.md.
>
> **Zweck:** Vorbereitung des KlangweltReview (TodoList Block B). Reine Code-Aufruf-Inventur der Sound-Trigger-Seite — die WAV-Datei-Inventur ist separat in `MCK_Dateischema.md`, die ElevenLabs-Prompts liegen in `MCK_Prompts.md`.
>
> **Methodik:** Strukturierung nach Datei (nicht nach Funktionskategorie). Diskussion „braucht jede Stelle einen Sound?" folgt in claude.ai. Hier nur Sammlung.
>
> **Bekannte Mini-Befunde aus TodoList nicht erneut rapportiert:** Der `.erledigteEntfernt`-Fallback auf `.teilaufgabeUebersprungen` (`SoundManager.swift` Z. 113) ist in `MyCadenza_TodoList_v1_7.md` bereits dokumentiert (F11-Verifikation 02.05.2026, Querbezug zu KlangweltReview). Auch der `audioPlayer` als Single-Instance (Build 10728 / B-S2 Sound-Kaskaden) ist bekannt. Beide tauchen unten nur dort auf, wo sie für die akustische Diskussion frisch relevant sind.

---

## Sektion 1 — Bereits gespielte Sounds

### `ChrisMeinTag/SoundManager.swift`

Zentrale Audio-Engine. Außer den hier aufgeführten privaten Aufrufen wird der SoundManager an keiner anderen Stelle direkt mit AVFoundation/AudioToolbox-APIs umgangen — sämtliche Sound-Wiedergabe der App läuft über diese Datei.

**Z. 229–230 — Audio Session Setup**
```swift
try AVAudioSession.sharedInstance().setCategory(.ambient, mode: .default)
try AVAudioSession.sharedInstance().setActive(true)
```
- **Sound:** keiner direkt
- **Trigger:** Einmalig im `private init()` des `SoundManager.shared` (App-Start, MainActor)
- **Annotation:** `.ambient` ist die einzige Audio-Session-Konfiguration der App. Wird nach Init nicht erneut gesetzt — siehe Sektion 3 zu Wechselwirkung mit MusicKit.

**Z. 369–372 — Bundle-Lookup mit Fallback (Preview)**
```swift
let url: URL? = Bundle.main.url(forResource: fileName, withExtension: "wav")
    ?? action.fallback.flatMap {
        Bundle.main.url(forResource: "\(world.rawValue)_\($0.rawValue)", withExtension: "wav")
    }
```
- **Sound:** WAV-Datei via `previewBundleSound(...)`
- **Trigger:** `preview(action:world:volume:)` aus SoundtestView (manueller Test)
- **Annotation:** Eigene Code-Pfade für Preview vs. Workflow — Preview übergeht `isEnabled` und Haptik (siehe Doc-Comment Z. 347–354).

**Z. 412/417 — System-Sounds**
```swift
AudioServicesPlayAlertSound(action.systemSoundID)

if action == .tagKomplett {
    DispatchQueue.main.asyncAfter(deadline: .now() + 0.3) {
        AudioServicesPlayAlertSound(action.systemSoundID)
    }
}
```
- **Sound:** iOS-System-Sound nach `SoundAction.systemSoundID`
- **Trigger:** `playSystemSound(action:)` — wird gerufen, wenn Klangwelt = `.system` ODER WAV nicht ladbar (Fallback)
- **Annotation:** `tagKomplett` triggert den Ton zweimal mit 0.3s Versatz. Doppelton-Logik nur für diese eine Aktion.

**Z. 429–430 — Bundle-Lookup mit Fallback (Workflow)**
```swift
let url: URL? = Bundle.main.url(forResource: fileName, withExtension: "wav")
    ?? action.fallback.flatMap { Bundle.main.url(forResource: "\(world.rawValue)_\($0.rawValue)", withExtension: "wav") }
```
- **Sound:** WAV-Datei via `playBundleSound(...)`
- **Trigger:** Reguläre `play(action:world:isHighPriority:)` aus allen Convenience-Methoden
- **Annotation:** Lautstärke wird mit `priorityFactor` (1.0 high / 0.85 normal) skaliert — eine zweite, ungezählte Lautstärke-Differenzierung neben den `SoundVolume`-Stufen.

**Z. 461–540 — Haptik-Schicht**
```swift
private func triggerHaptic(for action: SoundAction, isHighPriority: Bool) {
    switch action {
    case .tagBegonnen, .tagBeendet:
        let generator = UIImpactFeedbackGenerator(style: .medium)
        generator.impactOccurred()
    // ... 16 weitere case-Branches
    }
}
```
- **Sound:** kein Sound, sondern haptisches Feedback (16 Aktions-Mappings auf `UIImpactFeedbackGenerator`/`UINotificationFeedbackGenerator`)
- **Trigger:** Wird aus `play(action:world:isHighPriority:)` immer mitgerufen — auch wenn `isEnabled == false`, sofern `hapticEnabled == true` (Z. 392–395)
- **Annotation:** Haptik ist 1:1 an den Sound-Aufruf gekoppelt. Es gibt keine Möglichkeit, Sound auszulösen ohne Haptik (außer im Preview-Pfad). `tagKomplett` triggert die Haptik wie den Sound zweifach (Z. 470–476).

---

### `ChrisMeinTag/CadenzaApp.swift`

App-Lifecycle. Hier sitzen die zwei „Tag"-bezogenen Sound-Trigger und die Notification-Berechtigung.

**Z. 23 — Notification-Authorization**
```swift
TaskScheduler.registerBackgroundTask()
UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) { _, _ in }
```
- **Sound:** mittelbar — `.sound`-Option ermöglicht, dass UN-Notifications Sounds spielen
- **Trigger:** App-Start (`init()`)
- **Annotation:** Authorization wird unbedingt jedes Mal ohne Bedingung beim Start angefordert. Pop-up zeigt iOS automatisch nur beim ersten Mal.

**Z. 101 — `playDayStarted()`**
```swift
guard todayString != lastDaySoundDate else { return }
lastDaySoundDate = todayString
SoundManager.shared.playDayStarted()
```
- **Sound:** `.tagBegonnen` in `dayBookendSoundWorld` (kategorieunabhängig)
- **Trigger:** `runProcessing()` über `playDayStartSoundIfNeeded()` — einmal pro Kalendertag pro Gerät beim ersten App-Öffnen oder ersten Foreground-Wechsel des Tages
- **Annotation:** AppStorage-gestützte Einmaligkeit pro Tag.

**Z. 111 — `playTaskExpired(category:)`**
```swift
guard result.expiredCount > 0 else { return }
DispatchQueue.main.asyncAfter(deadline: .now() + 1.5) {
    SoundManager.shared.playTaskExpired(category: result.representativeCategory)
}
```
- **Sound:** `.aufgabeVerfallen` in der Klangwelt der Repräsentanten-Kategorie
- **Trigger:** Nach `runProcessing()` wenn `processAllTasks` ≥ 1 verfallene Aufgabe meldet — mit 1.5s Verzögerung, damit Tag-Beginn-Sound zuerst ausklingen kann
- **Annotation:** Repräsentanten-Logik (hoch-priorisierte gewinnt, sonst erste verfallene) bestimmt die Kategorie. Mehrere Verfallereignisse → ein Sound.

---

### `ChrisMeinTag/TodayView.swift`

Zentrale Aufgaben-Workflow-View. Mit Abstand die meisten Sound-Aufrufe — Tagesübergang, Sub-/Hauptaufgaben-Status, Tagesbericht.

**Z. 384 — `playDayComplete()`**
```swift
.onChange(of: doneTaskCount) { oldValue, newValue in
    if newValue == totalTaskCount && totalTaskCount > 0 && !hasPlayedCompletionSound {
        SoundManager.shared.playDayComplete()
        hasPlayedCompletionSound = true
    }
    if newValue < totalTaskCount {
        hasPlayedCompletionSound = false
    }
}
```
- **Sound:** `.tagKomplett` in `dayBookendSoundWorld` (Doppelton)
- **Trigger:** Wenn der erledigt-Counter den Tagessoll erreicht (Fortschrittsring 100 %); Reset des Flags wenn Counter wieder fällt
- **Annotation:** Lokales Flag verhindert mehrfaches Auslösen; Reset bei Rückgang erlaubt Wiederholung am selben Tag, falls Aufgabe wieder geöffnet wird.

**Z. 586/598 — `playTaskDue` / `playTaskOverdue` (Übergangs-Detektion)**
```swift
let newlyDue = allTasks.filter { ... task.startTime > previousNow && task.startTime <= newNow ... }
if let representative = newlyDue.first(where: { $0.isHighPriority }) ?? newlyDue.first {
    SoundManager.shared.playTaskDue(
        category: representative.category,
        isHighPriority: representative.isHighPriority
    )
}
// ... analog für newlyOverdue → playTaskOverdue
```
- **Sound:** `.aufgabeFaellig` bzw. `.aufgabeUeberfaellig` in der Klangwelt der Repräsentanten-Kategorie
- **Trigger:** `playTransitionSounds(from:to:)` aus `.onChange(of: scenePhase)` (`.active`) — pro Übergangs-Typ ein Klang, Repräsentant via High-Priority
- **Annotation:** Nur scenePhase-Wechsel triggert das. Bei länger geöffneter App ohne Background-Wechsel werden Übergänge nicht akustisch ausgelöst (siehe Sektion 4).

**Z. 906 — `playDayEnded()`**
```swift
.onAppear {
    SoundManager.shared.playDayEnded()
}
```
- **Sound:** `.tagBeendet` in `dayBookendSoundWorld`
- **Trigger:** `DayReportView.onAppear` — wird gespielt jedes Mal, wenn das Tagesbericht-Sheet erscheint
- **Annotation:** Bei wiederholtem Antippen des Fortschrittsrings (Z. 280) klingt der Sound jedes Mal erneut. Siehe Sektion 3.

**Z. 1339/1341 — `playOverdueCompleted` / `playMainTaskComplete` (Inline-Subtask-Kaskade)**
```swift
InlineSubTaskRow(subTask: subTask, parentTask: task, onSync: {
    let previousStatus = task.status
    task.syncStatus()
    if previousStatus != .done && task.status == .done {
        if task.expiryTime <= Date() {
            SoundManager.shared.playOverdueCompleted(category: task.category, isHighPriority: task.isHighPriority)
        } else {
            SoundManager.shared.playMainTaskComplete(category: task.category, isHighPriority: task.isHighPriority)
        }
    }
}, ...)
```
- **Sound:** `.ueberfaelligErledigt` oder `.hauptaufgabeKomplett` (Kaskade!)
- **Trigger:** Inline-Teilaufgaben-Toggle in `TodayTaskRowView` triggert über `onSync` die Kaskaden-Sound-Logik, wenn die Hauptaufgabe dadurch komplett wird
- **Annotation:** Direkt nach dem Sound der Teilaufgabe (`playSubTaskDone` Z. 1471) — möglicher Doppel-Sound binnen Millisekunden bei der letzten Teilaufgabe (siehe Sektion 4 / B-S2).

**Z. 1450/1453/1468/1471 — Inline-Subtask-Toggle**
```swift
Button {
    if subTask.status == .skipped {
        subTask.status = .open
        SoundManager.shared.playTaskReset(category: parentTask.category)
    } else {
        subTask.status = .skipped
        SoundManager.shared.playSubTaskSkipped(category: parentTask.category, isHighPriority: parentTask.isHighPriority)
    }
    onSync()
}
// ... Done-Button analog mit playTaskReset / playSubTaskDone
```
- **Sound:** `.aufgabeZurueckgesetzt` / `.teilaufgabeUebersprungen` / `.teilaufgabeErledigt`
- **Trigger:** Inline-Forward- und Check-Button in `InlineSubTaskRow` (TodayView)
- **Annotation:** Toggle-Logik: Status `.skipped/.done` → wieder `.open` spielt Reset-Sound; sonst der jeweilige Status-Sound.

**Z. 1548/1550 — `playOverdueCompleted` / `playMainTaskComplete` (Subtask-Sheet-Kaskade)**
```swift
SubTaskRowView(... onSync: {
    let previousStatus = task.status
    task.syncStatus()
    if previousStatus != .done && task.status == .done {
        if task.expiryTime <= Date() {
            SoundManager.shared.playOverdueCompleted(category: task.category, isHighPriority: task.isHighPriority)
        } else {
            SoundManager.shared.playMainTaskComplete(category: task.category, isHighPriority: task.isHighPriority)
        }
    }
}, ...)
```
- **Sound:** dito Kaskade
- **Trigger:** `SubTaskSheetView` (Sheet, Z. 1536–1652) — Status-Toggle einer Teilaufgabe, der die Hauptaufgabe komplettiert
- **Annotation:** Zweiter Aufrufweg derselben Kaskaden-Sound-Logik.

**Z. 1639/1641 — `playOverdueCompleted` / `playMainTaskComplete` (Subtask-Editor-Sheet-Kaskade)**
```swift
.sheet(item: $editingSubTask) { subTask in
    SubTaskEditorSheet(subTask: subTask, parentTask: task, onSync: {
        let previousStatus = task.status
        task.syncStatus()
        if previousStatus != .done && task.status == .done {
            if task.expiryTime <= Date() {
                SoundManager.shared.playOverdueCompleted(...)
            } else {
                SoundManager.shared.playMainTaskComplete(...)
            }
        }
    })
}
```
- **Sound:** dito Kaskade
- **Trigger:** Schließen des SubTask-Edit-Sheets im Sheet-Stack mit fertiger Hauptaufgabe
- **Annotation:** Dritter Aufrufweg — siehe `MainTask+Actions.swift`-Doc-Comment Z. 8–22 zur historischen Dreifach-Duplizierung dieser Logik.

**Z. 1700/1703/1718/1721 — Subtask-Toggle im SubTaskSheet**
```swift
// SubTaskRowView (Sheet-Variante, Z. 1683–1756)
Button {
    if subTask.status == .skipped { subTask.status = .open
        SoundManager.shared.playTaskReset(...)
    } else { subTask.status = .skipped
        SoundManager.shared.playSubTaskSkipped(...)
    }
    onSync()
}
// Done-Button mit playTaskReset / playSubTaskDone analog
```
- **Sound:** `.aufgabeZurueckgesetzt` / `.teilaufgabeUebersprungen` / `.teilaufgabeErledigt`
- **Trigger:** Skip- und Check-Button in der Sheet-Variante der `SubTaskRowView`
- **Annotation:** Inhaltlich identisch zur Inline-Variante Z. 1450–1471. Funktional zwei separate Views derselben Logik.

**Z. 1823/1825/1827 — `SubTaskEditorSheet.commit()`**
```swift
if draftStatus != subTask.status {
    subTask.status = draftStatus
    if draftStatus == .done {
        SoundManager.shared.playSubTaskDone(category: parentTask.category, isHighPriority: parentTask.isHighPriority)
    } else if draftStatus == .skipped {
        SoundManager.shared.playSubTaskSkipped(category: parentTask.category, isHighPriority: parentTask.isHighPriority)
    } else {
        SoundManager.shared.playTaskReset(category: parentTask.category)
    }
}
```
- **Sound:** `.teilaufgabeErledigt` / `.teilaufgabeUebersprungen` / `.aufgabeZurueckgesetzt`
- **Trigger:** Status-Picker im SubTask-Editor-Sheet, beim „Speichern"
- **Annotation:** Status-Wechsel über Picker (segmented), nicht via Toggle-Buttons. Das `commit()` ruft danach `onSync()`, das bei Hauptaufgaben-Komplettierung zusätzlich den Kaskaden-Sound an Z. 1639/1641 auslöst — möglicher Doppel-Sound (Subtask + Hauptaufgabe).

---

### `ChrisMeinTag/MainTask+Actions.swift`

Konsolidierte MainTask-Operationen mit Sound-Kopplung (Build 10716).

**Z. 58 — `playCompletedRemoved`**
```swift
isManualStatus = false
syncStatus()
try? context.save()
SoundManager.shared.playCompletedRemoved(category: category)
```
- **Sound:** `.erledigteEntfernt` in Kategorien-Klangwelt
- **Trigger:** `cleanupCompletedSubTasks(context:)` — Confirmation-Dialog „Erledigte Teilaufgaben entfernen?" (TodayView Z. 1361, 1647; EditorView Z. 845)
- **Annotation:** Bekannt aus TodoList F11-Befund: in einer Klangwelt fehlt die WAV, fällt auf `.teilaufgabeUebersprungen` zurück.

**Z. 77 — `playTaskReset` (resetAllSubTasks)**
```swift
markOpen(manually: false)
try? context.save()
SoundManager.shared.playTaskReset(category: category)
```
- **Sound:** `.aufgabeZurueckgesetzt`
- **Trigger:** Confirmation-Dialog „Alle Teilaufgaben zurücksetzen?" (TodayView Z. 1650; EditorView Z. 848)

**Z. 91 — `playTaskReset` (performMarkOpen)**
```swift
func performMarkOpen() {
    markOpen()
    SoundManager.shared.playTaskReset(category: category)
}
```
- **Sound:** `.aufgabeZurueckgesetzt`
- **Trigger:** Status-Menü „wieder öffnen" einer erledigten/übersprungenen Hauptaufgabe (Aufrufer in TodayView/EditorView; konkrete Stelle nicht hier markiert, geht über das Status-Menu der Aufgaben-Zeilen)

**Z. 107/109/111 — Drei-Wege-Verzweigung in `performMarkDone(context:)`**
```swift
let hasSubTasks = !(subTasks ?? []).isEmpty
if expiryTime <= Date() {
    SoundManager.shared.playOverdueCompleted(category: category, isHighPriority: isHighPriority)
} else if hasSubTasks {
    SoundManager.shared.playMainTaskComplete(category: category, isHighPriority: isHighPriority)
} else {
    SoundManager.shared.playMainTaskDone(category: category, isHighPriority: isHighPriority)
}
```
- **Sound:** `.ueberfaelligErledigt` / `.hauptaufgabeKomplett` / `.hauptaufgabeErledigt`
- **Trigger:** `performMarkDone` — manuelles „Hauptaufgabe erledigen" via Status-Menu
- **Annotation:** Drei-Wege-Logik — dieselbe Methode entscheidet abhängig von Status und Teilaufgaben-Existenz. Gleiche Verzweigung wie in TodayView Z. 1338–1342 (dort allerdings via `task.syncStatus()` als Kaskade).

**Z. 125 — `playMainTaskSkipped`**
```swift
func performMarkSkipped(context: ModelContext) {
    markSkipped()
    TaskScheduler.cancelReminder(for: self)
    TaskScheduler.scheduleNextOccurrence(for: self, context: context)
    try? context.save()
    SoundManager.shared.playMainTaskSkipped(category: category, isHighPriority: isHighPriority)
}
```
- **Sound:** `.hauptaufgabeUebersprungen`
- **Trigger:** Status-Menu „Hauptaufgabe überspringen"

---

### `ChrisMeinTag/EditorView.swift`

**Z. 599 — `playTaskCreated` (NewTaskView)**
```swift
modelContext.insert(task)
if hasReminder { TaskScheduler.scheduleReminder(for: task) }
SoundManager.shared.playTaskCreated(category: selectedCategory)
dismiss()
```
- **Sound:** `.aufgabeErstellt` in Kategorien-Klangwelt
- **Trigger:** „Speichern"-Button im Sheet „Neue Aufgabe" (Z. 563)
- **Annotation:** `selectedCategory` kann nil sein → `.system`-Klangwelt.

**Z. 897 — `playTaskDeleted` (deleteAllInstances)**
```swift
private func deleteAllInstances() {
    SoundManager.shared.playTaskDeleted(category: task.category)
    let groupID = task.repeatGroupID
    if let allTasks = try? modelContext.fetch(FetchDescriptor<MainTask>()) {
        for t in allTasks where t.repeatGroupID == groupID { modelContext.delete(t) }
    }
    ...
}
```
- **Sound:** `.aufgabeGeloescht`
- **Trigger:** Confirmation-Dialog „Aufgabe löschen" → Bestätigen (Z. 841)
- **Annotation:** Sound spielt VOR der eigentlichen Löschung — bewusste Reihenfolge oder Zufall? Bei großem `repeatGroupID`-Set kann das Sound-Timing relevant werden.

---

### `ChrisMeinTag/CategoryManagerView.swift`

**Z. 115 — `playTaskDeleted` (CategoryManagerView.performDelete)**
```swift
private func performDelete(_ category: TaskCategory) {
    SoundManager.shared.playTaskDeleted(category: category)
    for task in category.tasks ?? [] { task.category = nil }
    ...
}
```
- **Sound:** `.aufgabeGeloescht` in Klangwelt der zu löschenden Kategorie
- **Trigger:** Löschen einer Kategorie aus dem Manager
- **Annotation:** Klangwelt der GELÖSCHTEN Kategorie — die Klangwelt verschwindet im selben Moment. Akustisches Abschiednehmen.

**Z. 237 — `playTaskCreated` (Neue Kategorie)**
```swift
ToolbarItem(placement: .confirmationAction) {
    Button(String(localized: "Speichern")) {
        let category = TaskCategory(name: name, color: color, icon: icon, sortOrder: existing.count, soundWorld: soundWorld)
        modelContext.insert(category)
        SoundManager.shared.playTaskCreated(category: category)
        dismiss()
    }
}
```
- **Sound:** `.aufgabeErstellt` in der GERADE GEWÄHLTEN Klangwelt der neuen Kategorie
- **Trigger:** „Speichern"-Button im Sheet „Neue Kategorie"
- **Annotation:** Erste Hörprobe der neu gewählten Klangwelt.

**Z. 348 — `playTaskDeleted` (EditCategoryView.performDelete)**
```swift
private func performDelete() {
    SoundManager.shared.playTaskDeleted(category: category)
    for task in category.tasks ?? [] { task.category = nil }
    ...
}
```
- **Sound:** `.aufgabeGeloescht`
- **Trigger:** „Löschen"-Button in EditCategoryView (Trash-Icon Toolbar) → Confirmation
- **Annotation:** Doppelung zu Z. 115 — beide Pfade (CategoryManagerView und EditCategoryView) löschen Kategorien.

---

### `ChrisMeinTag/CreateTaskFromTemplateView.swift`

**Z. 222 — `playTaskCreated`**
```swift
modelContext.insert(task); try? modelContext.save()
if hasReminder { TaskScheduler.scheduleReminder(for: task) }
SoundManager.shared.playTaskCreated(category: selectedCategory)
dismiss(); onSaved?()
```
- **Sound:** `.aufgabeErstellt`
- **Trigger:** „Speichern"-Button beim Erstellen einer Aufgabe aus Template
- **Annotation:** Identisch zur NewTaskView in EditorView Z. 599 — Aufgabe-aus-Template gilt akustisch wie Aufgabe-erstellt.

---

### `ChrisMeinTag/TemplateView.swift`

**Z. 311 — `playTaskCreated` (saveTemplate)**
```swift
modelContext.insert(template)
// ... subTasks-Loop ...
SoundManager.shared.playTaskCreated(category: selectedCategory)
dismiss()
```
- **Sound:** `.aufgabeErstellt`
- **Trigger:** „Speichern"-Button in „Neues Template"-Sheet
- **Annotation:** Dieselbe SoundAction für „neue Aufgabe" und „neues Template" und „neue Kategorie" — siehe Sektion 3.

---

### `ChrisMeinTag/SettingsView.swift`

**Z. 545 — `playExportImport` (Export erfolgreich)**
```swift
.fileExporter(...) { result in
    switch result {
    case .success:
        showingExportSuccess = true
        SoundManager.shared.playExportImport()
    case .failure(let error): print("Export fehlgeschlagen: ...")
    }
}
```
- **Sound:** `.exportImport` in `dayBookendSoundWorld`
- **Trigger:** Erfolgreich abgeschlossener `.fileExporter`-Flow
- **Annotation:** Bei Fehler kein Sound (siehe Sektion 2).

**Z. 720 — `playExportImport` (Import erfolgreich)**
```swift
let result = try CadenzaDataImporter.importData(...)
importResultMessage = String(localized: ...)
showingImportResult = true
SoundManager.shared.playExportImport()
```
- **Sound:** `.exportImport`
- **Trigger:** Erfolgreich abgeschlossener Import (sowohl Replace als auch Merge-Mode)

**Z. 866 — UN-Notification `.default` (DailySummary)**
```swift
let content = UNMutableNotificationContent()
content.title = String(localized: "Dein Tag mit MyCadenza")
content.body = String(localized: "Schau dir deine heutigen Aufgaben an!")
content.sound = .default
```
- **Sound:** iOS-Standard-Notification-Sound (.default)
- **Trigger:** `DailySummaryScheduler.schedule(hour:minute:)` plant tägliche Zusammenfassung
- **Annotation:** Aktuell nicht aktivierbar — Toggle Z. 226 ist `.disabled(true)` mit „Bald verfügbar"-Badge. Code ist da, UI noch nicht freigeschaltet. Siehe Sektion 3.

---

### `ChrisMeinTag/TaskScheduler.swift`

**Z. 334 — UN-Notification `.default` (Reminder)**
```swift
let content = UNMutableNotificationContent()
content.title = task.isHighPriority ? "❗ \(task.title)" : task.title
content.body = String(localized: "Aufgabe ist jetzt fällig")
content.sound = .default
```
- **Sound:** iOS-Standard-Notification-Sound (.default)
- **Trigger:** `scheduleReminder(for:)` — wenn `task.hasReminder == true` und `startTime > now`
- **Annotation:** Wird beim Erstellen (NewTaskView, CreateTaskFromTemplateView) und beim Toggle in EditTaskView (Z. 831) gerufen. Funktioniert technisch, obwohl der zentrale Settings-Toggle „Erinnerungen aktiv" disabled ist (siehe Sektion 3).

---

### `ChrisMeinTag/SoundtestView.swift`

**Z. 90 — `preview(action:world:volume:)`**
```swift
private func playSound(action: SoundAction, world: SoundWorld) {
    lastPlayed = "\(world.rawValue)_\(action.rawValue)"
    SoundManager.shared.preview(action: action, world: world, volume: volume)
}
```
- **Sound:** beliebige `SoundAction` × Klangwelt × Lautstärke (Test)
- **Trigger:** Play-Button in `SoundActionRow` — manueller Test, kein Workflow
- **Annotation:** Eigener Code-Pfad ohne `isEnabled`/Haptik-Schicht (siehe SoundManager Z. 347–354). Siehe Sektion 4 zur Frage, ob das in der Inventur als „Sound" zählt.

**Z. 111/116 — Bundle-Existenz-Checks**
```swift
if Bundle.main.url(forResource: primary, withExtension: "wav") != nil {
    return .wavExists
}
if let fallback = action.fallback {
    let fallbackName = "\(world.rawValue)_\(fallback.rawValue)"
    if Bundle.main.url(forResource: fallbackName, withExtension: "wav") != nil {
        return .fallbackWav
    }
}
```
- **Sound:** keiner — reine Existenzprüfung für UI-Status (🟢/🟡/🔴)
- **Trigger:** Ableitung des `FileStatus` für jede `SoundActionRow`

---

## Sektion 2 — Trigger-Stellen ohne Sound

### `ChrisMeinTag/EditorView.swift`

**Z. 605 ff. — EditTaskView (Bearbeiten bestehender Aufgaben)**
```swift
struct EditTaskView: View {
    @Bindable var task: MainTask
    ...
    var body: some View {
        Form {
            Section(String(localized: "Allgemein")) {
                TextField(String(localized: "Titel"), text: $task.title)
                Picker(...) { ... }
                Toggle(String(localized: "Aufgabe aktiv"), isOn: $task.isActive)
            }
            ...
        }
    }
```
- **Was passiert dort:** Bestehende Aufgabe wird live über `@Bindable` editiert — Titel, Wiederholung, Kategorie, Zeiten, Priorität, Erinnerung, Farbe. Kein „Speichern"-Button, persistiert wird via SwiftData-Auto-Save / dismiss.
- **Warum Kandidat:** Schwesterview zu NewTaskView (Z. 599 spielt `playTaskCreated`). Eine geänderte Aufgabe ist akustisch indistinguishable von „nichts passiert". Update-Sound diskutieren.

**Z. 791 — „Teilaufgabe hinzufügen" Inline-Button (EditTaskView)**
```swift
Button {
    newSubTaskEntries.append("")
    focusedNewEntryIndex = newSubTaskEntries.count - 1
} label: { Label(String(localized: "Teilaufgabe hinzufügen"), systemImage: "plus") }
```
- **Was passiert dort:** Neuer leerer Eingabe-Slot wird angefügt; bei `saveNewEntry(at:)` (Z. 862) wird die Teilaufgabe persistiert
- **Warum Kandidat:** Hinzufügen einer Teilaufgabe ist ein Prozess-Schritt (Aufgabe wird detaillierter), aktuell stumm. AddSubTaskView (Sheet, Z. 966–980) ebenso stumm.

**Z. 808 — „Template erstellen" (`saveAsTemplate()`)**
```swift
Button { saveAsTemplate() } label: {
    Label(String(localized: "Template erstellen"), systemImage: "doc.badge.plus")
}
// saveAsTemplate Z. 883: kein Sound
```
- **Was passiert dort:** Bestehende Aufgabe wird als neues Template dupliziert
- **Warum Kandidat:** TemplateView Z. 311 spielt beim Erstellen eines Templates `playTaskCreated`. Hier wird auch ein Template erstellt, aber stumm.

**Z. 829 — `.onChange(of: task.category)`**
```swift
.onChange(of: task.category) { _, newCategory in if let newCategory { task.color = newCategory.color } }
```
- **Was passiert dort:** Aufgaben-Kategorie wird gewechselt — damit ändert sich gleichzeitig die Klangwelt der Aufgabe
- **Warum Kandidat:** Klangwelt-Wechsel hat akustische Bedeutung (alle Folgesounds dieser Aufgabe klingen anders), wird aber nicht akustisch markiert.

**Z. 837 — Trash-Button (öffnet Confirmation)**
```swift
ToolbarItem(placement: .navigationBarTrailing) {
    Button { showingDeleteConfirmation = true } label: { Image(systemName: "trash").foregroundStyle(.red) }
}
```
- **Was passiert dort:** Confirmation-Dialog „Aufgabe löschen" wird angezeigt
- **Warum Kandidat:** Trigger einer destruktiven UI ohne Sound — Bestätigungsdialog erscheint stumm. Sound erst NACH Bestätigung in `deleteAllInstances` Z. 897.

**Z. 190/218/775 — `.onMove` (Reorder)**
```swift
.onMove { source, destination in moveTasksInGroup(group.tasks, from: source, to: destination) }
.onMove(perform: moveTasks)
.onMove(perform: moveSubTasks).deleteDisabled(true)
```
- **Was passiert dort:** Drag-and-Drop zwischen Aufgaben/Subtasks innerhalb des Editors; Reihenfolge wird in `sortOrder` persistiert
- **Warum Kandidat:** Reorder-Aktion ist eine bewusste Manipulation der Liste, derzeit stumm.

**Z. 814–824 — DEBUG: „TEST: Als gestern erledigt markieren"**
```swift
#if DEBUG
Section(String(localized: "Entwicklung")) {
    Button(String(localized: "TEST: Als gestern erledigt markieren")) {
        task.markDone()
        ...
    }.foregroundStyle(.orange)
}
#endif
```
- **Was passiert dort:** DEBUG-Helper, manipuliert Status und `lastExecutedDate`
- **Warum Kandidat:** Vermutlich bewusst stumm. Notiert für Vollständigkeit.

---

### `ChrisMeinTag/TemplateView.swift`

**EditTemplateView (ab Z. 318) — `@Bindable` live edit**
- **Was passiert dort:** Template-Eigenschaften werden live editiert ohne Speichern-Button
- **Warum Kandidat:** Analog zu EditTaskView — Update-Sound diskutieren.

**Z. 21 — `.onMove(perform: moveTemplates)` (Template-Liste)**
**Z. 419 — `.onMove(perform: moveSubTasks)` (Template-Subtasks)**
- **Was passiert dort:** Template-Reorder bzw. Template-Subtask-Reorder
- **Warum Kandidat:** Reorder stumm.

**Z. 552 — AddSubTaskView in Template (`saveSubTasks`)**
```swift
Button(String(localized: "Hinzufügen")) {
    saveSubTasks()
}
.disabled(entries.allSatisfy { $0.isEmpty })
// saveSubTasks Z. 560: kein Sound
```
- **Was passiert dort:** Neue Template-Teilaufgaben werden hinzugefügt
- **Warum Kandidat:** Analog zu EditorView Z. 966 — stumm.

---

### `ChrisMeinTag/CategoryManagerView.swift`

**EditCategoryView (ab Z. 249) — `@Bindable` live edit**
- **Was passiert dort:** Name, Farbe, Icon, **Klangwelt** der Kategorie werden geändert ohne Speichern-Sound
- **Warum Kandidat:** Klangwelt-Wechsel ist akustisch hochrelevant — keine Sound-Markierung beim Speichern.

**Z. 39 — `.onMove(perform: moveCategories)`**
- **Was passiert dort:** Kategorie-Reihenfolge per Drag-and-Drop ändern
- **Warum Kandidat:** Stumm.

---

### `ChrisMeinTag/TodayView.swift`

**Z. 280 — Tap auf ProgressRingView öffnet Tagesbericht**
```swift
Button {
    showingDayReport = true
} label: { ProgressRingView(...) }
```
- **Was passiert dort:** Sheet `DayReportView` wird präsentiert
- **Warum Kandidat:** Sound `playDayEnded()` wird erst beim `.onAppear` des Sheet gespielt (Z. 906). Tap auf den Ring selbst ist stumm — könnte als Trigger gedacht sein, oder das `.onAppear` ist der gewollte Sound-Moment. Klärung erwünscht.

**Z. 298 — Tap auf CloudKit-StatusDot wechselt zur Settings-iCloud-Section**
```swift
Button { onRequestSettings() } label: {
    HStack(spacing: 6) { CloudKitStatusDot(...); ... }
}
```
- **Was passiert dort:** Tab-Wechsel zur SettingsView mit Scroll-Anker
- **Warum Kandidat:** Tab-Wechsel an sich vermutlich nicht sound-würdig, aber als Pattern erwähnt.

**Z. 392/403/1622/1623/1633 — Sheet-Öffnungen**
```swift
.sheet(isPresented: $showingDayReport) { DayReportView(...) }
.sheet(isPresented: $showMusicBrowser) { MusicBrowserSheet(manager: musicManager) }
.sheet(isPresented: $showingAddSubTask) { AddSubTaskView(task: task) }
.sheet(isPresented: $showingEditTask) { ... EditTaskView ... }
.sheet(item: $editingSubTask) { subTask in SubTaskEditorSheet(...) }
```
- **Was passiert dort:** Modale Sheets erscheinen
- **Warum Kandidat:** Sheet-Übergang als generelles Trigger-Muster — derzeit stumm. Diskussion: Sheet-Öffnen ist eher Navigation als Workflow-Schritt.

**Z. 418/456/487 — Section-Header Toggle (expand/collapse)**
```swift
SectionHeaderView(... onToggle: { sectionExpanded[sectionID] = !expanded })
```
- **Was passiert dort:** Aufgaben-Sektion wird auf-/zugeklappt
- **Warum Kandidat:** UI-Manipulation ohne Sound — wahrscheinlich richtig stumm, aber als Pattern erwähnt.

**Z. 450/482 — `.onMove` (Aufgaben-Reorder)**
- **Was passiert dort:** Aufgaben werden zwischen Positionen verschoben
- **Warum Kandidat:** Wie überall, Reorder stumm.

**Z. 1356/1646/1649 — ConfirmationDialogs ohne eigenen Sound**
- Cleanup-Bestätigung (zweimal) und Reset-Bestätigung
- **Was passiert dort:** Dialog erscheint, Sound spielt erst bei „Entfernen"/„Zurücksetzen" über `cleanupCompletedSubTasks` / `resetAllSubTasks`

**Z. 1557 — `.onMove(perform: moveSubTasks)` (Subtask-Reorder im Sheet)**
- Stumm wie alle anderen `.onMove`.

**Z. 320 — MusicPlayerBar in Heute-Header**
```swift
MusicPlayerBar(manager: musicManager, onBrowse: { showMusicBrowser = true })
```
- **Was passiert dort:** Apple-Music-Steuerung (Play/Pause/Skip/Browse) — eigene Audio-Wiedergabe
- **Warum Kandidat:** Eigenes Audio-System (MusicKit), das von SoundManager getrennt läuft. Diskussion ggf. interessant, ob die Cadenza-Sounds und Apple-Music-Wechsel akustisch koordiniert sein sollten (z. B. Ducken).

---

### `ChrisMeinTag/SettingsView.swift`

**Z. 220–230 — Notification-Toggles `.disabled(true)`**
```swift
Toggle(isOn: $notificationsEnabled) {
    Label(String(localized: "Erinnerungen aktiv"), systemImage: "bell.fill")
}
.tint(...)
.disabled(true)

Toggle(isOn: $dailySummaryEnabled) {
    Label(String(localized: "Tägliche Zusammenfassung"), systemImage: "sun.max.fill")
}
.disabled(true)
```
- **Was passiert dort:** Toggles existieren, sind aber UI-seitig blockiert. „Bald verfügbar"-Badge.
- **Warum Kandidat:** Noch kein Trigger, aber der Code für `scheduleReminder` und `DailySummaryScheduler` läuft ohne diesen Toggle bereits — siehe Sektion 3.

**Z. 244 — Toggle „Töne"**
```swift
Toggle(isOn: $soundManager.isEnabled) { Label(String(localized: "Töne"), ...) }
```
- **Was passiert dort:** Sound-System wird global aktiviert/deaktiviert
- **Warum Kandidat:** Beim Aktivieren könnte ein kurzer „Hello"-Sound als Bestätigung der gewählten Klangwelt sinnvoll sein (Vorhören). Aktuell stumm.

**Z. 250 — Picker „Lautstärke"**
**Z. 256 — Picker „Klangwelt Tag-Beginn / Tag-Ende"**
- **Was passiert dort:** Lautstärke-/Klangwelt-Wechsel
- **Warum Kandidat:** Vorhören wäre logisch (man wählt eine akustische Eigenschaft), passiert aber nicht.

**Z. 263 — Toggle „Haptisches Feedback"**
- **Was passiert dort:** Haptik-System an/aus
- **Warum Kandidat:** Bestätigungs-Tap bei Aktivierung (kurzer `UIImpactFeedbackGenerator`) wäre denkbar.

**Z. 291 — „Verbinden" Apple Music**
```swift
Button(String(localized: "Verbinden")) {
    Task { await musicManager.authorize() }
}
```
- **Was passiert dort:** Apple-Music-Authorization-Flow
- **Warum Kandidat:** Erfolgreiche Verbindung könnte ein Bestätigungs-Sound sein.

**Z. 309 — „Musik durchsuchen" (öffnet MusicBrowserSheet)**
- Stumm.

**Z. 332 — CloudKitStatusDot (in iCloud-Section, anzeigend)**
- **Was passiert dort:** Reine Status-Anzeige
- **Warum Kandidat:** Status-Wechsel von `.syncing` → `.synced` oder `.failed` (siehe CloudKitStatusObserver) sind akustisch nirgendwo markiert. Diskussion: nervig oder hilfreich?

**Z. 352–376 — Daten-Buttons**
```swift
Button { prepareExportPreview() } label: { Label(String(localized: "Daten exportieren"), ...) }
Button { showingImportPicker = true } label: { Label(String(localized: "Daten importieren"), ...) }
Button { prepareDuplicateCheck() } label: { Label(String(localized: "Duplikate bereinigen"), ...) }
Button(role: .destructive) { showingResetConfirmation = true } label: { Label(String(localized: "Alle Daten zurücksetzen"), ...) }
```
- **Was passiert dort:** Buttons öffnen Vorschau-Alerts (Export/Import/Duplikate) bzw. Confirmation (Reset)
- **Warum Kandidat:** Tap → Alert ohne Sound. Sound erst nach erfolgreichem Export/Import (Z. 545/720). Duplikate-Bereinigung (`performDuplicateCleanup` Z. 763) ist KOMPLETT stumm — kein Sound, obwohl sie destruktiv löscht. „Alle Daten zurücksetzen" (`resetAllData` Z. 834) ebenfalls stumm — destruktive Aktion ohne akustische Bestätigung.

**Z. 395 — „Was ist neu" Button (öffnet WhatsNewView)**
- Stumm.

**Z. 401/411 — Datenschutz/Support-Links (extern)**
- Stumm — externe Browser-Aktion.

**Z. 445/451 — „Präsentationsmodus aktivieren / beenden"**
- **Was passiert dort:** Modus-Wechsel der App (verlangt Restart)
- **Warum Kandidat:** Wahrscheinlich richtig stumm, weil App neugestartet wird.

**Z. 467 — NavigationLink „Sound-Test" (DEBUG)**
- **Was passiert dort:** Öffnet SoundtestView
- **Warum Kandidat:** Ironisch stumm — die Sound-Test-View, deren Inhalt ja Sounds sind, hat selbst keinen Öffnungs-Sound.

**Z. 473 — „WhatsNew-Flag zurücksetzen" (DEBUG)**
- Stumm.

---

### `ChrisMeinTag/ContentView.swift`

**Z. 60 — SplashView**
```swift
if showingSplash {
    SplashView()
        .transition(.opacity)
        .zIndex(1)
}
```
- **Was passiert dort:** App-Start-Splash mit Logo
- **Warum Kandidat:** App-Start ist akustisch erst beim ersten App-Öffnen des Tages markiert (`playDayStarted`, mit Verzögerung über `runProcessing`). Splash selbst stumm.

**Z. 65 — `.onAppear` mit `playDayStartSoundIfNeeded`-Indirektion**
- (Sound spielt im CadenzaApp, nicht hier — aber der visuelle Trigger ist hier)

**Z. 84–94 — Update-Alert**
```swift
.alert(String(localized: "Update verfügbar"), isPresented: $updateChecker.showUpdateAlert) {
    Button(String(localized: "Aktualisieren")) { UIApplication.shared.open(updateChecker.appStoreURL) }
    Button(String(localized: "Später"), role: .cancel) { }
} message: { Text(...) }
```
- **Was passiert dort:** Alert „Update verfügbar"
- **Warum Kandidat:** Alert erscheint stumm; iOS-System könnte einen Alert-Sound spielen, aber kein App-eigener.

**Z. 95 — `.fullScreenCover` Onboarding**
```swift
.fullScreenCover(isPresented: Binding(
    get: { !hasSeenOnboarding && !showingSplash },
    set: { if !$0 { hasSeenOnboarding = true } }
)) {
    OnboardingView(hasSeenOnboarding: $hasSeenOnboarding)
}
```
- **Was passiert dort:** Onboarding-Cover beim ersten App-Start
- **Warum Kandidat:** Erstkontakt mit der App — derzeit ohne akustische Begleitung.

**Z. 101 — `.sheet(isPresented: $showWhatsNew) { WhatsNewView() }`**
- **Was passiert dort:** WhatsNewView nach Update
- **Warum Kandidat:** Update-Erfahrung ist stumm; könnte ein einmaliger „Neu da!"-Klang sein.

---

### `ChrisMeinTag/OnboardingView.swift`

**Z. 22 — TabView Page-Wechsel (`@State currentPage`)**
**Z. 81–93 — „Weiter"-Button (`currentPage += 1`)**
**Z. 96–104 — „Überspringen"-Button (`hasSeenOnboarding = true`)**
**Z. 69–79 — „Los geht's"-Button (auf Last-Page)**
- **Was passiert dort:** Onboarding-Screen-Wechsel und Abschluss
- **Warum Kandidat:** Onboarding-Schritte ohne akustische Begleitung. Bei „Los geht's" ist die App akustisch live — einen Übergangs-Klang könnte man dort setzen.

**WhatsNewView Z. 232 — „Verstanden"-Button (dismiss)**
- Stumm — Schließen ohne Akustik.

---

### `ChrisMeinTag/UpdateChecker.swift`

**Z. 71 — `showUpdateAlert = true`**
```swift
await MainActor.run {
    availableVersion = storeVersion
    showUpdateAlert = true
}
```
- **Was passiert dort:** Trigger des Update-Alerts in ContentView
- **Warum Kandidat:** Wenn ein neues Update entdeckt wird, könnte ein dezenter Hinweis-Sound passen — aktuell stumm.

---

### `ChrisMeinTag/CloudKitStatusObserver.swift`

**Z. 204 — `currentStatus = .syncing`**
**Z. 214 — `currentStatus = .synced(at: date)`**
**Z. 222 — `currentStatus = .failed(at: date)`**
**Z. 236 — Initial-Timeout → `.localOnly`**
**Z. 254 — Active-Sync-Timeout → `.failed`**
- **Was passiert dort:** CloudKit-Sync-Status wechselt; UI-Punkt ändert Farbe (CloudKitStatusDot)
- **Warum Kandidat:** Vor allem `.failed` (Sync-Fehler) ist eine Stelle, an der akustisches Feedback dem Nutzer Information geben könnte. `.synced` nach `.syncing` (erfolgreicher Initial-Sync) wäre eine andere Kandidaten-Stelle. Aktuell alle Status-Wechsel stumm.

---

### `ChrisMeinTag/TaskScheduler.swift`

**Z. 64 — `processAllTasks(context:force:)`**
- **Was passiert dort:** Kern-Verarbeitungslauf — Verfall, neue Wiederholungen, Duplikate, Badge
- **Warum Kandidat:** Spielt selbst keine Sounds (das macht CadenzaApp anhand des Rückgabewerts für Verfall). Andere Side-Effects stumm: neue Wiederholung erstellt, Duplikat bereinigt, doesNotExpire-Aufgabe deaktiviert. Diskussion: sind das hörbare Vorgänge oder „App räumt auf, Nutzer merkt nicht"-Vorgänge?

**Z. 320 — `if newTask.hasReminder { scheduleReminder(for: newTask) }` in `createNextIfNeeded`**
- **Was passiert dort:** Folge-Instanz wird erstellt, Reminder wird automatisch gesetzt
- **Warum Kandidat:** Stumm — gewollt, weil Hintergrund-Vorgang.

**Z. 348 — `cancelReminder(for:)`**
- **Was passiert dort:** Reminder wird entfernt (z. B. bei `markDone`)
- **Warum Kandidat:** Stumm — interner Vorgang.

**Z. 355 — `updateBadge(context:)`**
- Stumm — App-Icon-Badge.

**Z. 380 — `registerBackgroundTask` / Background-Task-Wakeup**
- **Was passiert dort:** iOS weckt App im Hintergrund
- **Warum Kandidat:** App im Hintergrund — Sound wäre nutzerfeindlich.

---

### Anmerkung — Validierungs-Architektur

> **Speichern-Buttons in Editor-Sheets sind durchgängig via `.disabled(...)` gesperrt**, solange Eingaben unvollständig sind (z. B. `.disabled(title.isEmpty)`). Dadurch entstehen keine Validierungsfehler-Trigger zur Laufzeit — der Fehlerfall ist im UI eingefangen, bevor er ein akustisches Feedback bräuchte. Das ist eine bewusste Designentscheidung, keine Lücke. Bestätigt in der Sound-Verdienst-Diskussion (claude.ai, 05.05.2026).

---

## Sektion 3 — Beobachtungen außerhalb des Auftrags

> Nicht beheben, nur dokumentieren.

**1. Filename- vs. Struct-Casing-Inkonsistenz (`SoundtestView.swift`)**
Die Datei heißt `SoundtestView.swift` (kleines „t") — der enthaltene struct-Typ heißt `SoundTestView` (großes „T"). Eine ähnliche Inkonsistenz wurde gerade erst (Commit `59aac74`, 04.05.2026) für die WAV-Subordner hygienisiert. Konsistenz beim nächsten Hygiene-Sweep prüfen.

**2. Audio-Session-Wechselwirkung MusicKit ↔ SoundManager**
SoundManager konfiguriert `.ambient` einmalig im Init (Z. 229–230) und setzt diese Kategorie nie wieder. `ApplicationMusicPlayer` (MusicManager Z. 91) nutzt intern eine eigene Session-Konfiguration (typischerweise `.playback`). Wenn der Nutzer Apple Music startet und währenddessen ein Cadenza-Sound spielen soll (z. B. nach `playSubTaskDone`), läuft die AVAudioSession potenziell in der Konfiguration, die MusicKit hinterlassen hat. Verhalten in diesem Mischfall nicht verifiziert — könnte erklären, warum manche Cadenza-Sounds bei laufender Apple Music gefühlt anders/leiser klingen, sofern überhaupt. Empfehlung: beim KlangweltReview gezielt mit laufender Apple Music testen.

**Thematische Hoheit (geklärt 05.05.2026):** MusicKit ist ein paralleles Audio-System parallel zum SoundManager. Es spielt Audio aus, gehört aber thematisch nicht in die Klangwelt-Hoheit (Hintergrundmusik vom Nutzer gewählt, nicht App-eigener Klang). Der frühere Sektion-1-Eintrag zu `MusicManager.swift` Z. 91 wurde daher nach Klärung in claude.ai am 05.05.2026 hierher verschoben — Apple-Music-Wiedergabe wird nicht als Klangwelt-Sound geführt.

**3. Notification-Code aktiv, UI-Toggle disabled**
SettingsView Z. 220 zeigt zwei Notification-Toggles als `.disabled(true)` mit „Bald verfügbar"-Badge. Gleichzeitig wird `TaskScheduler.scheduleReminder` aktiv aufgerufen, sobald eine Aufgabe `hasReminder = true` hat (NewTaskView Z. 598, EditTaskView Z. 831, CreateTaskFromTemplateView Z. 221). Der Notification-Sound (`.default`, TaskScheduler Z. 334) wird bei Reminder-Trigger gespielt, ohne dass der Nutzer den zentralen Settings-Toggle aktiviert hat. UI und Code sind nicht in Einklang. Für KlangweltReview relevant: System-Notification-Sound `.default` ist akustisch eine Stimme im Cadenza-Kosmos, ohne im SoundManager-Modell zu existieren.

**4. SoundAction `.aufgabeErstellt` / `.aufgabeGeloescht` semantisch dreifach belegt**
Drei verschiedene Trigger nutzen denselben Sound: Aufgabe erstellen (EditorView Z. 599, CreateTaskFromTemplateView Z. 222), Kategorie erstellen (CategoryManagerView Z. 237), Template erstellen (TemplateView Z. 311). Analog für Löschen (Aufgabe Z. 897, Kategorien-Manager Z. 115, EditCategoryView Z. 348). Diskussion fürs KlangweltReview: gewollte Klangidentität für „erstellt" allgemein, oder akustisch differenzierbare Sub-Klänge für Aufgabe vs. Kategorie vs. Template?

**5. Doppel-Sound-Risiko bei Hauptaufgaben-Komplettierung**
Wenn die letzte Teilaufgabe die Hauptaufgabe komplettiert, läuft folgende Sequenz:
- `playSubTaskDone(...)` (TodayView Z. 1471 oder 1721 oder 1823)
- direkt danach `task.syncStatus()` → `playMainTaskComplete(...)` (Z. 1341 / 1550 / 1641)

Beide Sounds werden binnen Millisekunden nacheinander gespielt. Der einzelne `audioPlayer` als Single-Instance (TodoList B-S2 / Build 10728) bedeutet aktuell, dass der Subtask-Sound durch den Hauptaufgaben-Sound überschrieben wird. Für die Diskussion „Sound-Kaskaden" beim KlangweltReview hörbar relevant, weil die Frage „beide Sounds nacheinander oder nur den finalen?" beantwortet werden muss, bevor B-S2 implementierbar ist.

**6. `playDayEnded` mehrfach abspielbar pro Tagesbericht-Öffnung**
TodayView Z. 906 ruft `playDayEnded()` in `.onAppear` der DayReportView. Bei wiederholtem Antippen des Fortschrittsrings (Sheet schließen → erneut öffnen) klingt der Sound jedes Mal. Möglicherweise gewollt; falls nicht, wäre ein Once-Per-App-Session-Flag analog zu `lastDaySoundDate` denkbar.

**7. Sound-Lautstärke-Differenzierung zweistufig statt einstufig**
Neben den drei `SoundVolume`-Stufen (`0.15 / 0.5 / 1.0`, Build 10727) skaliert `playBundleSound` zusätzlich mit `priorityFactor` (1.0 high / 0.85 normal, Z. 435–436). Effektive Volume-Matrix ist also 3 × 2 = 6 Stufen. Für die SoundtestView-Vorhörlogik nicht abgebildet (Preview übergibt Volume direkt, ohne Priority-Faktor). Diskussion: bewusst doppelt gespreizt, oder eher Konsequenz historischer Iterationen?

**8. `audioPlayer` Single-Instance auch zwischen Preview und Workflow geteilt**
SoundManager-Property `audioPlayer` (Z. 213) wird sowohl von `playBundleSound` (Workflow) als auch von `previewBundleSound` (Test) verwendet. Während eines Workflow-Sounds kann ein in der SoundtestView ausgelöster Preview den laufenden Workflow-Sound abschneiden — und umgekehrt. Vermutlich harmlos in der Praxis (man wechselt nicht zwischen Test und Workflow), aber notiert.

---

## Sektion 4 — Offene Fragen

**1. Gehört die SoundtestView-Preview (`SoundtestView.swift` Z. 90) in die Inventur als „bereits gespielter Sound"?**
Habe sie aufgenommen, weil sie technisch ein Sound-Trigger ist. Sie löst aber keinen Workflow-Sound aus, sondern nur Test-Wiedergabe. Bitte prüfen, ob sie in Sektion 1 sinnvoll platziert ist.

**2. Zählen UN-Notification-Sounds (`.default`) als „App-Sounds"?**
Zwei Stellen: TaskScheduler Z. 334 (Reminder) und SettingsView Z. 866 (DailySummary). Der eigentliche Sound wird von iOS gespielt, nicht von SoundManager. Ist das Teil der KlangweltReview-Hoheit (custom sound files möglich via `UNNotificationSound(named:)`) oder ein eigenes Thema? Habe sie in Sektion 1 mit Annotation aufgenommen.

**3. Gehört Apple-Music-Wiedergabe (`MusicManager.swift` Z. 91) in die Klangwelt-Diskussion?**
> **Beantwortet (claude.ai 05.05.2026):** Nein — siehe Sektion 3 Punkt 2 („Thematische Hoheit"). Apple-Music-Wiedergabe ist paralleles Audio-System, kein Klangwelt-Sound.

**4. Ist Drag-and-Drop / Reorder grundsätzlich als „Trigger-Stelle" gemeint, oder Stille hier richtig?**
Mehrere `.onMove`-Aufrufer (TodayView, EditorView, TemplateView, CategoryManagerView). Lange Drag-Bewegung mit jedem Pixel ein Klick wäre nervig — vermutlich richtig stumm. Falls aber der Drop-Moment markiert werden soll, sind die Stellen alle in Sektion 2 dokumentiert.

**5. Sheet-Öffnen als generelles Trigger-Muster?**
Es gibt sehr viele `.sheet(...)`-Aufrufe in der Codebasis (~12+). Aktuell alle stumm. Diskussion: Sheet-Öffnen als Navigation oder als Workflow-Schritt? Habe nicht jede Sheet-Stelle einzeln aufgeführt, nur die in TodayView und ContentView als Beispiele.

**6. Sind Validierungsfehler-Trigger gewollt, oder ist die Architektur „Button ist disabled bei ungültiger Eingabe" der bewusste Verzicht auf Fehler-Sounds?**
> **Beantwortet (claude.ai 05.05.2026):** Bewusster Verzicht — siehe Anmerkungs-Block am Ende von Sektion 2 („Validierungs-Architektur"). Speichern-Buttons sind durchgängig via `.disabled(...)` gesperrt; Validierungsfehler-Trigger entstehen zur Laufzeit nicht.

**7. Soll der allererste App-Start (nach Onboarding-Abschluss) akustisch von späteren Tag-Beginnen unterscheidbar sein?**
Aktuell gilt: Nach Onboarding-Abschluss läuft `runProcessing()` in CadenzaApp und spielt `playDayStarted()`. Es gibt keinen separaten „Welcome to MyCadenza"-Sound. Diskussion: gewollt, oder erstmaliger Klang anders gestalten?

**8. Soll WhatsNewView nach einem App-Update einen eigenen Klang bekommen?**
Aktuell stumm. Kandidaten: eigene SoundAction, oder Wiederverwendung von `.exportImport` (semantisch „App-weiter Erfolg"). Klassifizierung in claude.ai.

**9. Soll der Klangwelt-Wechsel im Settings-Picker (Z. 256) ein Vorhören auslösen?**
Würde die Auswahl direkter erfahrbar machen, ist aber bisher nicht implementiert. Lautstärke-Picker analog. Diskussion erwünscht.

**10. Gehören Status-Wechsel von CloudKitStatusObserver (`.syncing` / `.synced` / `.failed`) zur akustischen Hoheit?**
Alle fünf Status-Wechsel sind aktuell stumm. `.failed` wäre aus Nutzersicht relevant (Sync-Fehler), `.synced` nach `.syncing` zumindest beim ersten Mal pro Tag bestätigend. Aktuell bewusst stumm oder Lücke?

---

## Sektion 5 — Sound-Verdienst-Beschluss

> **Stand:** 06.05.2026 · Beschlossen in claude.ai-Sitzung. Diese Sektion ist die Arbeitsgrundlage für `MCK_Methodik.md` und die anschließende Cluster-für-Cluster Prompt-Überarbeitung in `MCK_Prompts.md`.

### Audio-Session-Konfiguration

Kategorie `.ambient` mit Option `.duckOthers` (statt `.mixWithOthers`). Hintergrundmusik (Apple Music, später Spotify) wird systemseitig gedimmt, sobald ein Klangwelt-Sound spielt; Dimm-Tiefe ist iOS-systemfest (~ -10 bis -15 dB). 

Volume-Resolution im SoundManager: bei `userVolume == .quiet && AVAudioSession.sharedInstance().isOtherAudioPlaying == true` wird effektiv auf `.normal` skaliert. Begründung: `.quiet` ist semantisch eine Quiet-Office-Stufe; mit Hintergrundmusik wäre Quiet praktisch unhörbar, der Use-Case der Quiet-Stufe greift in dem Kontext nicht.

### Neue SoundActions (sechs)

| Action (Arbeitsname) | Zweck | Trigger-Stellen |
|---|---|---|
| `.aktionRegistriert` | Level 1 — subtile Bestätigung, App hat die Eingabe entgegengenommen | Edit-Sheet schließt mit Änderung: `EditTaskView` (EditorView Z. 605 ff.), `EditTemplateView` (TemplateView Z. 318 ff.), `EditCategoryView` (CategoryManagerView Z. 249 ff.) — Cluster A. Nach destruktiver Aktion mit vorhergehender Warnung: EditorView Z. 837 Trash, TodayView Z. 1356/1646/1649 Cleanup × 2 + Reset — Cluster D Bestätigungs-Seite. |
| `.aktionAbgeschlossen` | Level 2 — vollere Erfolgsmeldung, App-globale oder externe Aktion durchgeführt | Apple-Music verbunden (SettingsView Z. 291), Duplikate bereinigt (`performDuplicateCleanup`, SettingsView Z. 763), Alle Daten zurücksetzen (`resetAllData`, SettingsView Z. 834), Daten-Export/Import-Erfolg (SettingsView Z. 545/720 — ersetzt das bisherige `playExportImport`). |
| `.warnungVorAktion` | Warn-Klang vor destruktiver Aktion (ConfirmationDialog erscheint) | EditorView Z. 837 Trash, TodayView Z. 1356/1646/1649 Cleanup × 2 + Reset, SettingsView Z. 355 Reset, SettingsView Z. 763 Duplikate. |
| `.updateVerfuegbar` | Hinweis-Klang, „etwas deutlicher als dezent" | UpdateChecker Z. 71 / ContentView Z. 84 — Update-Alert erscheint. |
| `.syncCompleted` | CloudKit Initial-Sync abgeschlossen | CloudKitStatusObserver Z. 214 — `.syncing → .synced` (initial). |
| `.syncFailed` | CloudKit Sync-Fehler | CloudKitStatusObserver Z. 222 — `.syncing → .failed`. |

Heuristik Level 1 vs. Level 2: subtil = einzelne Aufgabe / Inhalt-Veränderung; Erfolgsmeldung = App-globale Aktion / externes System.

### Bestehende Sounds — Verschiebungen und Ablösungen

- `playDayEnded` wird vom `.onAppear` des `DayReportView` (Z. 906) auf den Tap des Fortschrittsrings (TodayView Z. 280) verschoben. Sound reagiert auf den Trigger, nicht auf das Sheet.
- `playExportImport` (eigene SoundAction in der bisherigen Inventur) wird durch `.aktionAbgeschlossen` ersetzt. Bei Code-Implementierung deprecate-markieren oder entfernen — Detail-Entscheidung in der Methodik.

### Trigger-Logik — neue Sound-Wege

- Klangwelt-Wechsel im Picker (Settings-Tab UND `EditCategoryView`): Vorhören mit `tagBegonnen` der gewählten Welt.
- Toggle „Töne" aktivieren (SettingsView Z. 244): `tagBegonnen` der gewählten Welt — derselbe Mechanismus wie beim Picker. Begründung: der Nutzer hört direkt, wie die aktivierte Welt klingt; semantisch näher an „so klingt das jetzt" als an „Einstellung gespeichert".
- Toggle „Haptisches Feedback" aktivieren (SettingsView Z. 263): nur Haptik, kein Klang.
- Template erstellen via `saveAsTemplate` (EditorView Z. 808): `playTaskCreated`. Symmetrie zu TemplateView Z. 311, das ebenfalls `playTaskCreated` spielt.
- ConfirmationDialogs vor destruktiver Aktion bekommen Doppelmarkierung: `.warnungVorAktion` beim Erscheinen, `.aktionRegistriert` (kleinere Aktionen, Cluster D) bzw. `.aktionAbgeschlossen` (App-globale Aktionen, Cluster F) nach Durchführung.

### Bewusst stumm — Designentscheidung

- **Reorder / `.onMove`** quer durch alle Views (EditorView Z. 190/218/775; TemplateView Z. 21/419; CategoryManagerView Z. 39; TodayView Z. 450/482/1557).
- **Sheet-Öffnungen** (TodayView Z. 392/403/1622/1623/1633 und analog).
- **Validierungsfehler** — Disabled-Architektur ist die bewusste Designentscheidung (siehe Anmerkung Sektion 2).
- **App-Start: Splash, Onboarding-Schritte, „Los geht's", „Verstanden"** — der erste Tag-Beginn-Sound spielt erst durch die Tagesablauf-Logik in CadenzaApp, nicht durch App-Öffnen.
- **WhatsNewView** — Öffnen, Schließen, „Verstanden"-Button.
- **DEBUG-Buttons** (TEST-Markierungen, WhatsNew-Flag-Reset, Sound-Test-NavigationLink).
- **Tab-Wechsel** und **Section-Header Expand/Collapse**.
- **Aufgaben-Kategorie-Wechsel** (`.onChange(of: task.category)` in EditorView Z. 829) — Klangwelt-Wahl findet im Kategorie-Picker statt; der Aufgaben-Wechsel ist die Konsequenz, kein eigenständiger Wahl-Moment.
- **Teilaufgaben hinzufügen** (EditorView Z. 791/Z. 966; TemplateView Z. 552) — zu kleinteilig.
- **TaskScheduler-Side-Effects** (`processAllTasks` außer Verfall, `scheduleReminder`/`cancelReminder`, `updateBadge`, Background-Task-Wakeup) — Hintergrund-Vorgänge ohne Nutzer-Auslöser, Sound aus dem Nichts wäre verwirrend.

### Eigenes Implementations-Item

Push-Notifications (UN-Notification-Sounds) sollen Klangwelt-Identität bekommen — eigene Code-Etappe, weil eigene Audio-Session-Logik außerhalb der App-Foreground-Sounds. Aus diesem Sound-Verdienst-Beschluss ausgeklammert; bleibt als Backlog-Punkt im Klangwelt-Block der TodoList.

### Cluster A — technische Detail-Frage zur Methodik

Bei `@Bindable` mit Auto-Save persistiert jeder Tastendruck sofort. „Tatsächlich geändert" beim `.onDisappear` zu erkennen, braucht entweder einen Snapshot beim Öffnen + Diff beim Schließen oder ein `dirty`-Flag im ViewModel. Methodik-Entscheidung in `MCK_Methodik.md`, nicht in dieser Inventur-Sektion.

### Folge-Schritte

1. **`MCK_Methodik.md` befüllen** — 8-Slot-Schablone, ElevenLabs-Setup, Sound-Verdienst-Heuristik (Level-1 vs. Level-2), Robustheits-Konfiguration, Cluster-A-Dirty-Flag-Logik, bewusst-stille Trigger-Klassen, Haptik-Position.
2. **Code-Etappe Audio-Session-Robustheit** — `.ambient` mit `.duckOthers`, Volume-Resolution mit `isOtherAudioPlaying`, Pre-Loading.
3. **Cluster-für-Cluster Prompt-Überarbeitung in `MCK_Prompts.md`** — startet nach Methodik-Befüllung; ElevenLabs-WAVs werden komplett neu generiert nach den Designvorgaben aus Manifest, Chartas und Methodik.
