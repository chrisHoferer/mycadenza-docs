# MyCadenza – Projektstruktur & Referenz

> Dieses Dokument dient als Claude-Referenz für neue Sessions.
> Stand: 29.04.2026
> App Store: v1.6.1 (Build 10602) · In Entwicklung: v1.7.0 (Build 10716)

---

## 1. App-Überblick

**MyCadenza** ist eine persönliche Tagesplanungs-App für iOS/iPadOS.
Nutzer verwalten wiederkehrende Aufgaben mit Teilaufgaben, Kategorien und Klang-Feedback.

| Eigenschaft | Wert |
|---|---|
| App-Name | MyCadenza (DE + EN) |
| Bundle ID | `com.hoferer.ChrisMeinTag` |
| iCloud Container | `iCloud.com.hoferer.cmt` ⚠️ NICHT `iCloud.com.hoferer.ChrisMeinTag` (defekt) |
| Deployment Target | iOS/iPadOS 17.0+ |
| Framework | SwiftUI + SwiftData + CloudKit + MusicKit |
| Sprachen | Deutsch (Quelle), Englisch |
| Localization | `String(localized:)` mit deutschen Keys in `Localizable.xcstrings` (340 Keys) |
| Container-Architektur | Drei Modi (Produktion/Präsentation/Test) über `AppMode` + `PersistenceController` |

---

## 2. Dateiverzeichnis

### Views (Hauptansichten)

| Datei | Enthält | Beschreibung |
|---|---|---|
| `ContentView.swift` | `ContentView`, `SplashView` | TabView (4 Tabs: Heute, Aufgaben, Historie, Einstellungen) + Splash Screen + Onboarding-Trigger + WhatsNew-Sheet + UpdateChecker-Alert |
| `OnboardingView.swift` | `OnboardingView`, `WhatsNewView` | 4-Screen PageView beim ersten Start (AppStorage-gesteuert) + "Was ist neu"-Sheet nach Versionsupdate |
| `TodayView.swift` | `TodayView`, `TodayTaskRowView`, `ProgressRingView`, `SubTaskProgressRing`, `TaskSnapshotHelper`, `InlineSubTaskRow`, `SubTaskSheetView`, `SubTaskRowView` | Tagesansicht mit 5 Sektionen (Überfällig, Fällig, Anstehend, Ausblick, Abgeschlossen) |
| `EditorView.swift` | `EditorView`, `AufgabenListeView`, `TaskEditorRowView`, `AddTaskView`, `EditTaskView`, `AddSubTaskView`, `EditSubTaskView` | Aufgabenverwaltung, Erstellen/Bearbeiten von Aufgaben |
| `HistoryView.swift` | `HistoryView`, `HistoryTaskRowView` | Wochen/Tage/Kategorie-Hierarchie, read-only Ansicht erledigter Aufgaben |
| `SettingsView.swift` | `SettingsView`, `DailySummaryScheduler`, `CadenzaExportDocument` | Einstellungen, Export/Import, Klang, Darstellung, Verfallfrist, DEBUG-Block (Modus-Wechsel, WhatsNew-Reset) |
| `CategoryManagerView.swift` | `CategoryManagerView`, `CategoryRowView`, `AddCategoryView`, `EditCategoryView`, `IconPickerPreview`, `IconPickerGrid` | Kategorie-CRUD mit Farbpicker, Iconpicker, Klangwelt-Zuweisung |
| `TemplateView.swift` | `TemplateView`, `TemplateRowView`, `AddTemplateView`, `EditTemplateView`, `EditTemplateSubTaskView`, `AddTemplateSubTaskView` | Template-Verwaltung |
| `TemplatePickerView.swift` | `TemplatePickerView`, `TemplatePickerRow` | Auswahl eines Templates zur Aufgabenerstellung |
| `CreateTaskFromTemplateView.swift` | `CreateTaskFromTemplateView` | Aufgabe aus Template erstellen mit Anpassungsmöglichkeiten |

### Komponenten

| Datei | Enthält | Beschreibung |
|---|---|---|
| `ExpandableComponents.swift` | `SectionHeaderView`, `UnifiedCategoryHeader`, `DateGroupHeader`, `UnifiedSubTaskBadge`, `WeekHeaderView`, `DayHeaderView` | Einheitliche aufklappbare Header-Komponenten für alle Views |
| `PaletteColorPicker.swift` | `PaletteColorPicker`, `PaletteColorPreview` | Kuratierter Farbpicker mit 9×5 Farben, Light/Dark-Mode-fähig |

### Datenmodell

| Datei | Enthält | Beschreibung |
|---|---|---|
| `Models.swift` | `RepeatInterval` (enum), `CustomIntervalUnit` (enum), `TaskStatus` (enum), `SubTask` (@Model), `MainTask` (@Model) | Kern-Datenmodell: Aufgaben + Teilaufgaben + Enums + Status-API + Verfallfrist |
| `TaskCategory.swift` | `TaskCategory` (@Model) | Kategorien mit Farbe, Icon, Klangwelt; `deleteRule: .nullify` für Aufgaben und Templates |
| `TaskTemplate.swift` | `TemplateSubTask` (@Model), `TaskTemplate` (@Model) | Vorlagen für wiederkehrende Aufgaben |

### Modus & Persistenz

| Datei | Enthält | Beschreibung |
|---|---|---|
| `AppMode.swift` | `AppMode` (enum: production/presentation/test) | Modus-Verwaltung über UserDefaults; `current`, `set(...)`, `allowedStartDateRange`; `.test` ist DEBUG-only |
| `PersistenceController.swift` | `PersistenceController` (struct, singleton) | Zentraler `ModelContainer` pro Modus, getrennte Store-Pfade; CloudKit nur in `.production` |
| `DateMigrator.swift` | `DataMigrator` | Einmalige Migration: deutsche Enum-Rawvalues → englische (v1-Kompatibilität) |
| `DemoDataProvider.swift` | `DemoDataProvider` | Präsentationsmodus: lädt Demo-Daten in den Präsentations-Container, falls leer |

### Logik & Services

| Datei | Enthält | Beschreibung |
|---|---|---|
| `CadenzaApp.swift` | `CadenzaApp` (@main) | App-Einstiegspunkt, bezieht Container von `PersistenceController.shared`; vereinheitlichtes `runProcessing()` für Kaltstart und Vordergrund-Wechsel; Tag-Beginn-Sound, Verfall-Sound; CloudKit-Remote-Change nur in `.production` |
| `TaskScheduler.swift` | `TaskScheduler` (`@MainActor`) | Aufgabenverarbeitung, Wiederholungen, Erinnerungen, Badge, Background Refresh, Duplikat-Bereinigung, Verfallfrist-Check; Debounce 30 s, force-Override; Coalescing-Task für Remote-Sync-Bursts |
| `SoundManager.swift` | `SoundWorld` (enum, 5 Welten), `SoundAction` (enum, 17 Aktionen), `SoundVolume` (enum), `SoundManager` (singleton) | Klang-System: 5 Klangwelten × 17 Aktionen, AVAudioPlayer, Haptik |
| `AppPalette.swift` | `AppPalette`, `PaletteStop`, `PaletteFamily`, `Color` Extension | Farbsystem: 9 Familien × 5 Stufen, Gold-Chrome, Light/Dark-Auflösung, Hex-Konvertierung |
| `CadenzaExportImport.swift` | `CadenzaExport`, Export-Structs, `CadenzaDataExporter`, `CadenzaDataImporter`, `ImportError` | JSON-Export/Import des gesamten Datenbestands; CloudKit-Zwischen-Saves beim Replace-Import |

### Helper & Extensions

| Datei | Enthält | Beschreibung |
|---|---|---|
| `Date+MyCadenza.swift` | `Date.nextHalfHour(after:)` | Rundet auf die nächste volle halbe Stunde — Default-Startzeit beim Anlegen einer Aufgabe |
| `FormattingHelpers.swift` | `formatDuration(_:)` | Stunden-Wert → "30 Min." / "1 Std." / "1,5 Std." (locale-adaptive) |
| `MainTask+SubTaskOperations.swift` | `MainTask.cleanupCompletedSubTasks(context:)`, `MainTask.resetAllSubTasks(context:)` (`@MainActor`) | Konsolidierte Teilaufgaben-Operationen: war zuvor dreifach in EditTaskView/TodayTaskRowView/SubTaskSheetView dupliziert |

### Musik

| Datei | Enthält | Beschreibung |
|---|---|---|
| `MusicManager.swift` | `MusicManager` (`@Observable`) | MusicKit-Wrapper: Auth, Subscription-Status, isPlaying, Track-Metadaten, Steuerung |
| `MusicPlayerView.swift` | `RoutePickerView`, Musik-Leiste für TodayView | AirPlay-/Bluetooth-Picker (`AVRoutePickerView`-Wrapper) + Player-UI |

### Wartung & Test

| Datei | Enthält | Beschreibung |
|---|---|---|
| `UpdateChecker.swift` | `UpdateChecker` (`@Observable`) | iTunes-Lookup-API; max. 1×/Tag; Apple ID `6760941365`; Bundle ID `com.hoferer.ChrisMeinTag` |
| `SoundtestView.swift` | `SoundTestView` | Testbench für alle Sound-Aktionen × Klangwelten, gruppiert (Tagbezug, Aufgabenbezug, Settings) |

### Ressourcen

| Pfad | Beschreibung |
|---|---|
| `Sounds/` | WAV-Dateien (48 kHz): Unterordner `morgenwald/`, `salon/`, `cityflow/`, `Horizont/` mit `{klangwelt}_{aktion}.wav` (WAV-Review läuft, ca. 30 Dateien) |
| `Assets.xcassets` | App Icon (1024×1024), SplashLogo, LaunchBackground (#C8973A) |
| `Localizable.xcstrings` | Localization: Deutsche Keys → Englische Übersetzungen (340 Keys) |
| `ChrisMeinTag.entitlements` | iCloud Container, CloudKit, aps-environment |
| `Info.plist` | Launch Screen config, Background Modes, CloudKit |
| `CHANGELOG.md` | Versionshistorie |

---

## 3. Datenmodell (SwiftData)

### MainTask

| Feld | Typ | Beschreibung |
|---|---|---|
| `title` | `String` | Aufgabentitel |
| `color` | `String` | Hex-Farbwert (Light-Mode, z.B. "#378ADD") |
| `startTime` | `Date` | Startzeit |
| `validityDuration` | `Double` | Gültigkeitsdauer in Stunden |
| `repeatIntervalRaw` | `String` | Wiederholung (once/daily/workdays/weekly/monthly/custom) |
| `statusRaw` | `String` | Status (open/done/skipped) |
| `isManualStatus` | `Bool` | **Lock-Flag** gegen automatische Status-Überschreibung. `true` = `syncStatus()` lässt den Status unverändert. Wird durch `markDone/markSkipped` und `applyExpiry` gesetzt; `markOpen` entsperrt grundsätzlich. |
| `lastExecutedDate` | `Date?` | Zeitpunkt des letzten Statuswechsels auf `done`/`skipped`; konsistent aus `setStatus(...)` UND `syncStatus()` gepflegt; bei Reset auf `open` → `nil` |
| `isActive` | `Bool` | Aktiv/Inaktiv |
| `repeatGroupID` | `String` | UUID, verknüpft Wiederholungen einer Aufgabe |
| `customIntervalValue` | `Int` | Benutzerdefiniertes Intervall (Wert) |
| `customIntervalUnitRaw` | `String` | Benutzerdefiniertes Intervall (Einheit: days/workdays/weeks/months) |
| `sortOrder` | `Int` | Sortierung innerhalb der Kategorie |
| `hasReminder` | `Bool` | Erinnerung zur Startzeit |
| `isHighPriority` | `Bool` | Hohe Priorität (rotes Dreieck) |
| `doesNotExpire` | `Bool` | Kein Verfall (nur bei `.once`) |
| `subTasks` | `[SubTask]?` | Teilaufgaben (cascade delete) |
| `category` | `TaskCategory?` | Zugewiesene Kategorie |

**Berechnete Properties:** `expiryTime`, `repeatInterval`, `customIntervalUnit`, `status`, `derivedStatus`

**Methoden:**

| Methode | Beschreibung |
|---|---|
| `syncStatus()` | Übernimmt den `derivedStatus`, sofern kein Lock; pflegt `lastExecutedDate` bei Statuswechsel |
| `applyStatusToSubTasks()` | Propagiert den Hauptstatus auf alle Teilaufgaben (auch beim `.open`-Case seit Build 10712c) |
| `applyExpiry()` | Wird vom Scheduler aufgerufen, wenn die Verfallfrist abgelaufen ist; setzt `.skipped` mit Lock |
| `nextOccurrence(after:)` | Berechnet das Datum der nächsten Wiederholung |
| `graceEndTime(gracePeriodHours:)` | Ende der Grace-Period (zwischen `expiryTime` und automatischem Skipping) |
| `markDone(manually:)` | Setzt Status `.done`; lockt per Default |
| `markSkipped(manually:)` | Setzt Status `.skipped`; lockt per Default |
| `markOpen(manually:)` | Setzt Status `.open`; entsperrt grundsätzlich (Build 10712c). Der Parameter bleibt aus API-Symmetrie erhalten, ist aber wirkungslos. |
| `setStatus(_:manually:)` (private) | Gemeinsamer Helper: schreibt Status, Lock und `lastExecutedDate`, propagiert auf Teilaufgaben |

### SubTask

| Feld | Typ | Beschreibung |
|---|---|---|
| `title` | `String` | Titel |
| `statusRaw` | `String` | Status (open/done/skipped) |
| `parentTask` | `MainTask?` | Eltern-Aufgabe |
| `sortOrder` | `Int` | Sortierung |

### TaskCategory

| Feld | Typ | Beschreibung |
|---|---|---|
| `name` | `String` | Kategoriename |
| `color` | `String` | Hex-Farbwert (Light-Mode) |
| `icon` | `String` | SF Symbol Name |
| `sortOrder` | `Int` | Sortierung |
| `soundWorldRaw` | `String` | Klangwelt (system/morgenwald/salon/cityflow/horizont) |
| `tasks` | `[MainTask]?` | Verknüpfte Aufgaben (inverse, `deleteRule: .nullify`) |
| `templates` | `[TaskTemplate]?` | Verknüpfte Templates (inverse, `deleteRule: .nullify`) |

### TaskTemplate / TemplateSubTask

Gleiche Struktur wie MainTask/SubTask, aber ohne `status`, `isActive`, `lastExecutedDate`, `isManualStatus`, `repeatGroupID`. `doesNotExpire` ist vorhanden.

### Beziehungen

```
TaskCategory 1 ←→ N MainTask          (deleteRule: .nullify auf Kategorie-Seite)
TaskCategory 1 ←→ N TaskTemplate      (deleteRule: .nullify auf Kategorie-Seite)
MainTask     1 ←→ N SubTask           (deleteRule: .cascade)
TaskTemplate 1 ←→ N TemplateSubTask   (deleteRule: .cascade)
```

---

## 4. Design-System

### Farbpalette (AppPalette)

9 Familien: Blue, Teal, Amber, Coral, Purple, Pink, Green, Red, Gray
Je 5 Stops: 100 (hell) → 900 (dunkel)
Jeder Stop hat `lightHex` und `darkHex` — gespeichert wird immer `lightHex`.
`AppPalette.resolvedHex(hex, for: colorScheme)` gibt den passenden Hex-Wert zurück.

### Gold-Chrome-System

| Funktion | Verwendung |
|---|---|
| `goldStandard(for:)` | Hauptakzent: Toggles, Badges, Tint |
| `goldDark(for:)` | Überfällig, Übersprungen |
| `goldLight(for:)` | Highlights, Hover |
| `goldOriginalHex` | Splash Screen (#C8973A) |

### Aufklappbare Listen (ExpandableComponents.swift)

Einheitliches Muster für alle Views:
- **Chevron rechts** (▼/▲) auf jeder aufklappbaren Ebene
- **Tap auf gesamte Zeile** toggled (via `.contentShape(Rectangle())` + `.buttonStyle(.plain)`)

Komponenten:

| Komponente | Kontext |
|---|---|
| `SectionHeaderView` | TodayView Sektionen (Fällig, Ausblick, ...) |
| `UnifiedCategoryHeader` | Kategorie-Header in allen Views |
| `DateGroupHeader` | TodayView "Ausblick" Datum-Gruppen |
| `UnifiedSubTaskBadge` | Teilaufgaben-Zähler (mit optionalem onToggle) |
| `WeekHeaderView` | HistoryView Kalenderwochen |
| `DayHeaderView` | HistoryView Tage |

### Navigations-Übersicht

| View | Titel-Stil | Besonderheit |
|---|---|---|
| TodayView | Custom Listenzeile (28pt bold) | + Datum-Zeile + Fortschrittsring |
| EditorView (AufgabenListeView) | Custom Listenzeile (28pt bold) | Toolbar: Kategorie-Manager, Template-Manager, Plus |
| HistoryView | Custom Listenzeile (28pt bold) | Kein Toolbar |
| SettingsView | Standard `.navigationTitle` | `.inline` Titel |

Alle drei Hauptviews verwenden `.navigationBarTitleDisplayMode(.inline)` + eigene Titel-Section für einheitliche Textausrichtung.

---

## 5. Architektur-Muster

### Drei-Container-Architektur

`AppMode` × `PersistenceController` halten drei vollständig getrennte SwiftData-Stores:

| Modus | Store | CloudKit | Zweck |
|---|---|---|---|
| `.production` | Default-Pfad | ✅ `iCloud.com.hoferer.cmt` | Echtdaten |
| `.presentation` | `Application Support/MyCadenza/presentation.store` | — | Demo-Daten für App Store Screenshots |
| `.test` | `Application Support/MyCadenza/test.store` | — | Etappenspezifische Testszenarien (DEBUG-only) |

Der Modus wird in UserDefaults gespeichert. Der `ModelContainer` wird **einmal pro Prozess-Lifetime** gebaut — Moduswechsel erfordert App-Neustart. Foreground und Background-Task arbeiten am selben Container über `PersistenceController.shared`.

Im DEBUG-Build hat `SettingsView` einen eigenen Block für den Modus-Wechsel (`#if DEBUG`-Wraps); im Release-Build ist nur `.production` und `.presentation` erreichbar, der Wechsel-UI fehlt.

### Status-API auf MainTask

Direktes Schreiben des `status`-Setters lockt nicht mehr und pflegt kein `lastExecutedDate`. Status-Änderungen laufen ausschließlich über:

- `markDone(manually:)` / `markSkipped(manually:)` — lockt per Default
- `markOpen(manually:)` — entsperrt grundsätzlich, der Parameter ist wirkungslos
- `applyExpiry()` — Scheduler-Hook beim Ablauf der Verfallfrist
- `syncStatus()` — übernimmt den abgeleiteten Status nur, wenn `isManualStatus == false`

`applyStatusToSubTasks()` propagiert den Hauptstatus seit Build 10712c auch beim `.open`-Case symmetrisch auf die Teilaufgaben.

### Verfallfrist (Grace-Period)

Zwischen `MainTask.expiryTime` und `MainTask.graceEndTime(gracePeriodHours:)` ist die Aufgabe "überfällig aber offen" — der Nutzer kann sie noch erledigen. Erst nach Ablauf der Grace-Period setzt `TaskScheduler.processAllTasks` sie über `applyExpiry()` auf `.skipped` (mit Lock).

Die Frist liegt in UserDefaults unter `gracePeriodHours` (Default: 1.0), konfigurierbar in `SettingsView`.

### `@MainActor`-Konsistenz

Alle SwiftData-Operationen laufen auf dem MainActor, um Thread-Violations zu vermeiden:

- `TaskScheduler` ist `@MainActor`-isolierte Klasse (Build 10715, behebt TestFlight-Crash).
- `MainTask`-SubTask-Extension ist `@MainActor`-isoliert, weil sie auf `SoundManager.shared` zugreift (Build 10716).
- Der `BGTaskScheduler`-Closure läuft systemseitig auf einem Worker-Thread und hopt explizit per `Task { @MainActor in ... }` zurück, bevor `handleBackgroundTask` aufgerufen wird.
- Der `processAllTasks`-Debounce (30 s) und der `pendingRemoteChangeTask` (1,5 s Coalescing nach Remote-Sync-Bursts) sind durch die Klassen-Isolation race-frei.

### Konsolidierte Teilaufgaben-Operationen

`MainTask.cleanupCompletedSubTasks(context:)` und `MainTask.resetAllSubTasks(context:)` sind die einzige zulässige Aufrufstelle für Snapshot + Cleanup/Reset. Vor Build 10716 war diese Logik dreifach in `EditTaskView`, `TodayTaskRowView` und `SubTaskSheetView` dupliziert — mit subtilen Inkonsistenzen (fehlendes `isManualStatus = false`, fehlende Cleanup-/Reset-Sounds im Sheet). Views dürfen diese Operationen nicht mehr inline reproduzieren.

### Localization

- Alle UI-Strings: `String(localized: "Deutscher Key")`
- Keys sind **deutsch mit korrekten Umlauten** (ä, ö, ü, ß)
- Übersetzungen in `Localizable.xcstrings` (340 Keys, EN-Übersetzungen)
- ⚠️ Schutzwörter bei Umlaut-Operationen: "Neue", "zuerst" enthalten "ue", das KEIN Umlaut ist

### Datumsformatierung

- Locale-adaptive Formate: `formatter.setLocalizedDateFormatFromTemplate("EEEEdMMMM")`
- Nicht: `formatter.dateFormat = "EEEE, d. MMMM"` (wäre deutsches Hardcoding)
- Default-Startzeit beim Anlegen: `Date.nextHalfHour(after:)` aus `Date+MyCadenza.swift`

### Listen mit Edit-Mode

- `.environment(\.editMode, .constant(.active))` für Drag-to-Reorder
- `.swipeActions` funktioniert NICHT mit editMode → Context Menus + Trash Icons verwenden
- Navigation: `Button + .navigationDestination(item:)` statt `NavigationLink`

### Section-Hierarchie

Kategorien werden als echte `Section { } header:` verwendet → grauer Hintergrund.
Übergeordnete Header (Fällig, KW) werden als leere `Section { } header:` eingesetzt.
`.listSectionSpacing(.compact)` auf allen Listen für enge Abstände.

### Onboarding & WhatsNew

- 4-Screen PageView beim ersten Start (`OnboardingView.swift`), gesteuert über `@AppStorage("hasSeenOnboarding")`
- Nach Versionsupdate: `WhatsNewView` als Sheet, gesteuert über `@AppStorage("lastSeenWhatsNew")` (Versions-String-Vergleich)
- Neue Nutzer (frischer Onboarding-Lauf) bekommen WhatsNew nicht angezeigt — `lastSeenWhatsNew` wird beim ersten Start direkt auf die aktuelle Version gesetzt
- Im DEBUG-Build: Reset-Button für `lastSeenWhatsNew` in `SettingsView`

### Update-Checker

- `UpdateChecker` (`@Observable`) ruft beim App-Start und bei jedem `.active`-scenePhase-Wechsel die iTunes-Lookup-API auf
- Throttle: max. 1 Check pro Tag (UserDefaults-Datum)
- Bei verfügbarer neuerer Version → `.alert` in `ContentView` mit "Aktualisieren"-Button (öffnet App Store)

### Sound-System

`SoundManager` definiert **5 Klangwelten** und **17 Aktionen**:

**Klangwelten:** System, Morgenwald, Salon, Cityflow, Horizont. WAVs liegen unter `Sounds/{klangwelt}/{klangwelt}_{aktion}.wav` (WAV-Review läuft).

**Aktionen — Tagbezug:**
- `tagBegonnen` — App-Start, einmal pro Tag
- `tagKomplett` — alle Aufgaben des Tages erledigt
- `tagBeendet` — App-Ende (zukünftig)

**Aktionen — Aufgabenbezug:**
- `aufgabeFaellig` — Startzeit erreicht
- `aufgabeUeberfaellig` — Verfallzeit erreicht (Grace-Period startet)
- `aufgabeVerfallen` — Grace-Period abgelaufen, automatisch übersprungen
- `aufgabeErstellt` — neue Aufgabe angelegt
- `aufgabeGeloescht` — Aufgabe entfernt
- `erledigteEntfernt` — Cleanup von erledigten Teilaufgaben (`cleanupCompletedSubTasks`)
- `aufgabeZurueckgesetzt` — alle Teilaufgaben auf offen (`resetAllSubTasks`)

**Aktionen — Teil- und Hauptaufgabe:**
- `teilaufgabeErledigt`, `teilaufgabeUebersprungen`
- `hauptaufgabeErledigt`, `hauptaufgabeUebersprungen`, `hauptaufgabeKomplett`
- `ueberfaelligErledigt` — überfällige Aufgabe doch noch in der Grace-Period erledigt

**Aktionen — Settings:**
- `exportImport` — Export oder Import abgeschlossen

Klangwelt-Zuweisung erfolgt über die Kategorie (`TaskCategory.soundWorldRaw`). Tag-Beginn/Ende hat eine eigene globale Einstellung (`dayBookendSoundWorld`). Audio Session: `.ambient` (respektiert Silent-Switch). Aufgaben mit hoher Priorität werden lauter gespielt (Faktor 1.0 vs. 0.85). System-Sound-IDs sind fixiert: 1001 / 1054 / 1307.

### CloudKit-Sync

- Container: `iCloud.com.hoferer.cmt` (nur in `.production`)
- Sync via SwiftData `.cloudKitDatabase(.private(...))`
- Remote-Change-Listener: `NSPersistentStoreRemoteChange` → `TaskScheduler.scheduleRemoteChangeProcessing`
- Coalescing: Bursts werden zu einem einzigen Scan gebündelt (1,5 s nach letzter Notification)
- Duplikat-Bereinigung in `TaskScheduler.cleanupDuplicates()` zu Beginn jedes `processAllTasks`-Laufs
- Replace-Import legt CloudKit-Zwischen-Saves an, damit der Import bei großen Datenmengen nicht abreißt

---

## 6. Bekannte Fallstricke

| Problem | Lösung |
|---|---|
| iCloud Container `iCloud.com.hoferer.ChrisMeinTag` defekt | Immer `iCloud.com.hoferer.cmt` verwenden |
| `.swipeActions` + `.editMode(.constant(.active))` inkompatibel | Context Menus + Trash Icons |
| `String(localized:)` liest Bundle bei Launch, nicht bei `.environment(\.locale)` | Per-App Language via iOS Settings |
| `.navigationTitle` im Large-Title-Modus nicht an Listen-Content ausgerichtet | Custom Titel als erste Listenzeile |
| SwiftUI Button in Section Header wird blau gefärbt | `.buttonStyle(.plain)` |
| Spacer() in Buttons nicht tappbar | `.contentShape(Rectangle())` auf den HStack |
| `doesNotExpire` Tasks mit Zukunfts-Startdatum | Gehören in "Ausblick", nicht ausfiltern |
| Wörter mit "ue" die kein Umlaut sind | "Neue", "zuerst" schützen bei Batch-Umlaut-Operationen |
| Bundle Sounds nicht gefunden | Root-Level oder Klangwelt-Unterordner, kein `subdirectory` Parameter |
| App Store Version Train geschlossen | Version bump (z.B. 1.5.0 → 1.5.1) bei neuem Build nötig |
| `ModelContainer` wird nur 1× pro Prozess gebaut | Moduswechsel (AppMode) erfordert App-Neustart |
| SwiftData aus `BGTaskScheduler`-Closure crasht (Thread-Violation) | Explizit `Task { @MainActor in ... }` vor SwiftData-Zugriff |
| Direktes `task.status = .done` lockt nicht und pflegt kein `lastExecutedDate` | `markDone/markSkipped/markOpen` verwenden |
| SubTask-Cleanup-Logik inline in Views weicht voneinander ab | Nur `MainTask.cleanupCompletedSubTasks` / `resetAllSubTasks` aufrufen |
| MusicKit funktioniert nicht im Simulator | Auf echtem Gerät testen |

---

## 7. Aktueller Stand

- **App Store:** v1.6.1 (Build 10602)
- **In Entwicklung:** v1.7.0 (Build 10716)

### Implementiert in v1.7.0

- ✅ 4 Tab-Ansichten: Heute, Aufgaben, Historie, Einstellungen
- ✅ Aufgaben mit Teilaufgaben, Kategorien, Wiederholungen, Prioritäten
- ✅ Einheitliche aufklappbare Listen (Chevron + Tap Muster)
- ✅ "Ausblick" als vollwertige Sektion mit Datum-Gruppen
- ✅ Onboarding (4 Screens beim ersten Start)
- ✅ "Was ist neu"-Sheet nach Versionsupdate
- ✅ App-Store-Update-Hinweis (`UpdateChecker`)
- ✅ Verfallfrist (Grace-Period) zwischen Verfall und automatischem Skipping
- ✅ Status-API auf `MainTask` mit Lock-Semantik (`markDone/markSkipped/markOpen`, `applyExpiry`)
- ✅ Drei-Container-Architektur (Produktion/Präsentation/Test)
- ✅ `@MainActor`-konsistenter Scheduler — TestFlight-Crash gefixt
- ✅ Konsolidierte SubTask-Operationen (Cleanup + Reset) auf `MainTask`
- ✅ Klang-System: 5 Klangwelten × 17 Aktionen (Cityflow + Horizont implementiert)
- ✅ MusicKit-Integration (Apple Music, AirPlay/Bluetooth-Picker, Player-Leiste in TodayView)
- ✅ Export/Import (JSON) mit CloudKit-Zwischen-Saves beim Replace-Import
- ✅ Localization DE + EN (340 Keys)
- ✅ Alle Umlaute korrekt (ä, ö, ü, ß) in Code und xcstrings
- ✅ CloudKit-Sync mit Remote-Change-Coalescing
- ✅ Präsentationsmodus mit eigenem Store
- ✅ SoundTestView (Testbench für alle Aktionen × Klangwelten)
- ✅ Farbpalette mit 9×5 kuratierten Farben + Light/Dark
- ✅ Datenschutzrichtlinie + Support-Kontakt verlinkt (live seit 21.04.2026)

### Geplant

Detaillierter Sprint-Plan und Befund-IDs:

- **Single Source of Truth (Sprint):** `MyCadenza_TodoList_v1_7.md`
- **Single Source of Truth (Befunde):** `MyCadenza_CodeReview_v1_7_0.md`
- Befund-Schema: A-M*/A-S*/A-A* (Block A, v1.7.0), B-* (Block B, v1.7.1), C-* (Feature Pool), D-* (Website), F1–F11 (Findings)

Offene größere Themen außerhalb der laufenden Sprint-Etappen:
- ⬜ `defaultValidityDuration`/`defaultRepeatInterval` AppStorage in `AddTaskView` ankoppeln
- ⬜ In-App Sprachauswahl (statt iOS Settings Redirect)
- ⬜ Tägliche Zusammenfassung als Push-Notification
- ⬜ Algorithmische Tagesmelodie (musikalisches Abbild des Tagesfortschritts)
- ⬜ Kontextuelle Hilfetexte in Views (Schicht 2 der Anwenderdoku)
- ⬜ Quick Start + Vollständige Doku auf GitHub Pages (Schicht 3+4)

---

## 8. Session-Checkliste für Claude

Bei jeder neuen Session:

1. **Diese Datei lesen** für Projektüberblick
2. **`MyCadenza_TodoList_v1_7.md` und `MyCadenza_CodeReview_v1_7_0.md` als Single Source of Truth** für aktuellen Sprint und Befund-Status konsultieren
3. **Betroffene Swift-Dateien anfordern** — nicht aus dem Gedächtnis arbeiten
4. **Komplette Dateien liefern** — Chris bevorzugt Full-File-Replacement statt Diffs (bei großen Dateien gezielte Edits)
5. **Umlaut-Regeln beachten**: Alle `String(localized:)` Keys mit korrekten Umlauten (ä, ö, ü, ß), aber "Neue" und "zuerst" schützen
6. **Iterativ arbeiten**: Ein Schritt → Testen → Nächster Schritt (Build-Stage-Methodik)
7. **Clean Build empfehlen** bei Localization-Änderungen (⌘⇧K, dann ⌘B)
8. **Status-Änderungen am `MainTask`** ausschließlich über `markDone/markSkipped/markOpen` — nie direktes `task.status = ...`
9. **SubTask-Cleanup/Reset** ausschließlich über `MainTask.cleanupCompletedSubTasks` / `resetAllSubTasks`
