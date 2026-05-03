# MyCadenza – Projektstruktur & Referenz

> Dieses Dokument dient als Claude-Referenz für neue Sessions.
> Stand: 03.05.2026
> App Store: v1.6.1 (Build 10602) · v1.7.0 (Build 10722) zur Apple-Prüfung eingereicht

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
| Localization | `String(localized:)` mit deutschen Keys in `Localizable.xcstrings` |
| App-Modus | Drei Container (`production` / `presentation` / `test`) über `AppMode` + `PersistenceController` |

---

## 2. Dateiverzeichnis

### Views (Hauptansichten)

| Datei | Enthält | Beschreibung |
|---|---|---|
| `ContentView.swift` | `ContentView`, `SplashView` | TabView (4 Tabs: Heute, Aufgaben, Historie, Einstellungen) + Splash Screen + Onboarding-Trigger + WhatsNew-Sheet (automatisch nach Update) + UpdateChecker-Alert |
| `OnboardingView.swift` | `OnboardingView`, `WhatsNewView` | 4-Screen PageView beim ersten Start (AppStorage-gesteuert) + „Was ist neu"-Sheet nach Versionsupdate |
| `TodayView.swift` | `TodayView`, `TodayTaskRowView`, `ProgressRingView`, `SubTaskProgressRing`, `TaskSnapshotHelper`, `InlineSubTaskRow`, `SubTaskSheetView`, `SubTaskRowView` | Tagesansicht mit 5 Sektionen (Überfällig, Fällig, Anstehend, Ausblick, Abgeschlossen) |
| `EditorView.swift` | `EditorView`, `AufgabenListeView`, `TaskEditorRowView`, `AddTaskView`, `EditTaskView`, `AddSubTaskView`, `EditSubTaskView` | Aufgabenverwaltung, Erstellen/Bearbeiten von Aufgaben |
| `HistoryView.swift` | `HistoryView`, `HistoryTaskRowView` | Wochen/Tage/Kategorie-Hierarchie, read-only Ansicht erledigter Aufgaben |
| `SettingsView.swift` | `SettingsView`, `DailySummaryScheduler`, `CadenzaExportDocument` | Einstellungen, Export/Import, Klang, Darstellung, Verfallfrist; eigene Endnutzer-Sektion „Demo & Präsentation"; „Was ist neu"-Button im Über-Block; gebündelte „Entwicklung"-Sektion (DEBUG) |
| `CategoryManagerView.swift` | `CategoryManagerView`, `CategoryRowView`, `AddCategoryView`, `EditCategoryView`, `IconPickerPreview`, `IconPickerGrid` | Kategorie-CRUD mit Farbpicker, Iconpicker, Klangwelt-Zuweisung |
| `TemplateView.swift` | `TemplateView`, `TemplateRowView`, `AddTemplateView`, `EditTemplateView`, `EditTemplateSubTaskView`, `AddTemplateSubTaskView` | Template-Verwaltung |
| `TemplatePickerView.swift` | `TemplatePickerView`, `TemplatePickerRow` | Auswahl eines Templates zur Aufgabenerstellung |
| `CreateTaskFromTemplateView.swift` | `CreateTaskFromTemplateView` | Aufgabe aus Template erstellen mit Anpassungsmöglichkeiten |

### Komponenten

| Datei | Enthält | Beschreibung |
|---|---|---|
| `ExpandableComponents.swift` | `SectionHeaderView`, `UnifiedCategoryHeader`, `DateGroupHeader`, `UnifiedSubTaskBadge`, `WeekHeaderView`, `DayHeaderView` | Einheitliche aufklappbare Header-Komponenten für alle Views |
| `PaletteColorPicker.swift` | `PaletteColorPicker`, `PaletteColorPreview` | Kuratierter Farbpicker mit 9×5 Farben, Light/Dark-Mode-fähig |
| `CloudKitStatusView.swift` | `CloudKitStatusDot`, Status-Text-Helper, Modal-Hinweis-Logik | Wiederverwendbare UI-Bausteine für den iCloud-Status-Indikator im Header und in der Einstellungen-Sektion (Build 10722) |

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
| `DemoDataProvider.swift` | `DemoDataProvider` | Präsentationsmodus: lädt Demo-Daten in den Präsentations-Container; bei jeder Aktivierung zurücksetzen über `pendingResetKey` (Build 10719) |

### Logik & Services

| Datei | Enthält | Beschreibung |
|---|---|---|
| `CadenzaApp.swift` | `CadenzaApp` (@main) | App-Einstiegspunkt, bezieht Container von `PersistenceController.shared`; vereinheitlichtes `runProcessing()` für Kaltstart und Vordergrund-Wechsel; Tag-Beginn-Sound, Verfall-Sound; CloudKit-Remote-Change nur in `.production` |
| `TaskScheduler.swift` | `TaskScheduler` (`@MainActor`) | Aufgabenverarbeitung, Wiederholungen, Erinnerungen, Badge, Background Refresh, Duplikat-Bereinigung, Verfallfrist-Check; Debounce 30 s, force-Override; Coalescing-Task für Remote-Sync-Bursts |
| `SoundManager.swift` | `SoundWorld` (enum, 5 Welten), `SoundAction` (enum, 17 Aktionen), `SoundVolume` (enum), `SoundManager` (singleton) | Klang-System: 5 Klangwelten × 17 Aktionen, AVAudioPlayer, Haptik. Klangwelten-Redesign nach v1.7.2 geplant. |
| `AppPalette.swift` | `AppPalette`, `PaletteStop`, `PaletteFamily`, `Color` Extension | Farbsystem: 9 Familien × 5 Stufen, Gold-Chrome, Light/Dark-Auflösung, Hex-Konvertierung |
| `CadenzaExportImport.swift` | `CadenzaExport`, Export-Structs, `CadenzaDataExporter`, `CadenzaDataImporter`, `ImportError` | JSON-Export/Import des gesamten Datenbestands; CloudKit-Zwischen-Saves beim Replace-Import |
| `CloudKitStatusObserver.swift` | `CloudKitStatusObserver` (Singleton, `@MainActor`, `ObservableObject`) | Erkennt iCloud-Sync-Aktivität über vier Mechanismen: UserDefaults-Marker, primär `eventChangedNotification`, sekundär `NSPersistentStoreRemoteChange` als Live-Signal, Timeouts (60 s Initial, 5 min Active-Sync); Initial-DB-Snapshot beim App-Start (Build 10722) |

### Helper & Extensions

| Datei | Enthält | Beschreibung |
|---|---|---|
| `Date+MyCadenza.swift` | `Date.nextHalfHour(after:)` | Rundet auf die nächste volle halbe Stunde — Default-Startzeit beim Anlegen einer Aufgabe |
| `FormattingHelpers.swift` | `formatDuration(_:)` | Stunden-Wert → „30 Min." / „1 Std." / „1,5 Std." (locale-adaptive) |
| `MainTask+Actions.swift` | `MainTask.cleanupCompletedSubTasks(context:)`, `MainTask.resetAllSubTasks(context:)`, `MainTask.performMarkOpen()`, `MainTask.performMarkDone(context:)`, `MainTask.performMarkSkipped(context:)` (`@MainActor`) | Konsolidierte Teilaufgaben-Operationen (Build 10716) plus Status-Aktionen mit Sound + Scheduler-Anbindung (Build 10717) |

### Musik

| Datei | Enthält | Beschreibung |
|---|---|---|
| `MusicManager.swift` | `MusicManager` (`@Observable`) | MusicKit-Wrapper: Auth, Subscription-Status, isPlaying, Track-Metadaten, Steuerung |
| `MusicPlayerView.swift` | `RoutePickerView`, Musik-Leiste für TodayView | AirPlay-/Bluetooth-Picker (`AVRoutePickerView`-Wrapper) + Player-UI |

### Wartung & Test

| Datei | Enthält | Beschreibung |
|---|---|---|
| `UpdateChecker.swift` | `UpdateChecker` (`@Observable`) | iTunes-Lookup-API; max. 1×/Tag; Apple ID `6760941365`; Bundle ID `com.hoferer.ChrisMeinTag` |
| `SoundtestView.swift` | `SoundTestView` | Testbench für alle Sound-Aktionen × Klangwelten, gruppiert (Tagbezug, Aufgabenbezug, Settings) — DEBUG-only |

### Ressourcen

| Pfad | Beschreibung |
|---|---|
| `Sounds/` | WAV-Dateien (48 kHz): Unterordner `morgenwald/`, `salon/`, `cityflow/`, `Horizont/` mit `{klangwelt}_{aktion}.wav` (WAV-Review läuft) |
| `Assets.xcassets` | App Icon (1024×1024), SplashLogo, LaunchBackground (#C8973A) |
| `Localizable.xcstrings` | Deutsche Keys → Englische Übersetzungen |
| `ChrisMeinTag.entitlements` | iCloud Container, CloudKit, aps-environment |
| `Info.plist` | Launch Screen config, Background Modes, CloudKit |
| `CHANGELOG.md` | Versionshistorie |

---

## 3. Klassen- und Methoden-Referenz

Granularität: Public/Internal API plus architektonisch wichtige private Helper. Keine Zeilenangaben (veralten zu schnell), keine vollständigen Signaturen außer wenn ein Default-Argument oder Parametername Design-Statement ist. View-Dateien sind nicht enthalten — siehe Top-Level-Tabelle in §2.

### Models.swift

**MainTask (@Model)**
- `syncStatus()`
- `applyStatusToSubTasks()`
- `applyExpiry()`
- `nextOccurrence(after:)`
- `markDone(manually: Bool = true)`, `markSkipped(manually: Bool = true)`, `markOpen(manually: Bool = true)`
- `graceEndTime(gracePeriodHours:)`
- `derivedStatus` (computed)
- *(privat)* `setStatus(_ target: TaskStatus, manually: Bool)` — gemeinsamer Helper für `markX`-Methoden, schreibt Status, Lock und `lastExecutedDate`, propagiert auf Teilaufgaben

**SubTask (@Model)**
- (nur Properties)

**Enums:** `RepeatInterval`, `CustomIntervalUnit`, `TaskStatus`

### TaskCategory.swift

**TaskCategory (@Model)**
- (nur Properties; `tasks` und `templates` mit `deleteRule: .nullify`)

### TaskTemplate.swift

**TaskTemplate (@Model)**
- (nur Properties)

**TemplateSubTask (@Model)**
- (nur Properties)

### MainTask+Actions.swift  [@MainActor extensions]

**SubTask-Operations**
- `cleanupCompletedSubTasks(context:)` — entfernt erledigte/übersprungene Teilaufgaben, erstellt Historie-Snapshot, re-sortiert, entsperrt Hauptaufgabe, spielt `playCompletedRemoved`
- `resetAllSubTasks(context:)` — setzt alle Teilaufgaben auf `.open`, erstellt Snapshot, spielt `playTaskReset`

**Status-Aktionen mit Sound + Scheduler**
- `performMarkOpen()` — `markOpen` + Reset-Sound
- `performMarkDone(context:)` — `markDone` + Sound (Überfällig-/Subtask-aware) + nächste Wiederholung über Scheduler
- `performMarkSkipped(context:)` — `markSkipped` + Sound + nächste Wiederholung

### Date+MyCadenza.swift

**Date (Extension)**
- `nextHalfHour(after:)` — rundet auf die nächste volle halbe Stunde

### FormattingHelpers.swift

- `formatDuration(_:)` — Stunden-Wert → lokalisierter String (z. B. „30 Min.", „1,5 Std.")

### CadenzaApp.swift

**CadenzaApp (@main)**
- `runProcessing()` — einheitlicher Verarbeitungs-Einstieg für Kaltstart und Vordergrund-Wechsel
- `playDayStartSoundIfNeeded()` — Tag-Beginn-Sound (einmal pro Tag)
- `playExpirySoundIfNeeded(result:)` — Verfall-Sound nach `processAllTasks`

### PersistenceController.swift

**PersistenceController (struct, singleton)**
- `makeConfiguration(for:schema:)` — `ModelConfiguration` pro Modus
- `storeURL(forFilename:)` — Pfad im Application Support Directory

### AppMode.swift

**AppMode (enum: production / presentation / test)**
- `current` (static) — liest aktiven Modus aus UserDefaults
- `set(_:)` — schreibt Modus in UserDefaults
- `allowedStartDateRange` (static) — Datumsbereich pro Modus
- `displayName`, `usesCloudKit`, `isDebugOnly` (computed)

### TaskScheduler.swift

**TaskScheduler (`@MainActor`)**
- `processAllTasks(context:force:)` — Hauptlauf: Verfall, Duplikate, Wiederholungen, Badge
- `scheduleRemoteChangeProcessing(context:)` — coalesciert CloudKit-Remote-Bursts (1,5 s)
- `ensureActiveOccurrences(context:allTasks:)` — Sicherheitsnetz gegen fehlende Nachfolger
- `createNextIfNeeded(for:context:)`, `scheduleNextOccurrence(for:context:)` — Wiederholungs-Erstellung
- `cleanupDuplicates(context:)`, `preservationScore(for:)` — Duplikat-Heuristik
- `scheduleReminder(for:)`, `cancelReminder(for:)` — Erinnerungs-Verwaltung
- `updateBadge(context:)` — App-Badge basierend auf offenen, fälligen Aufgaben
- `registerBackgroundTask()`, `scheduleBackgroundRefresh()`, `handleBackgroundTask(task:)` — `BGTaskScheduler`-Integration

### SoundManager.swift

**SoundManager (`@MainActor`, ObservableObject, singleton)**
- `configureAudioSession()` — `.ambient` + Active
- `playDayStarted()`, `playDayComplete()`, `playDayEnded()`
- `playTaskDue(category:isHighPriority:)`, `playTaskOverdue(category:isHighPriority:)`, `playTaskExpired(category:)`
- `playTaskCreated(category:)`, `playTaskDeleted(category:)`, `playCompletedRemoved(category:)`, `playTaskReset(category:)`
- `playSubTaskDone(category:isHighPriority:)`, `playSubTaskSkipped(category:isHighPriority:)`
- `playMainTaskDone(category:isHighPriority:)`, `playMainTaskSkipped(category:isHighPriority:)`, `playMainTaskComplete(category:isHighPriority:)`, `playOverdueCompleted(category:isHighPriority:)`
- `playExportImport()`
- *(privat)* `play(action:world:isHighPriority:)`, `playSystemSound(action:)`, `playBundleSound(action:world:isHighPriority:)`, `soundWorld(for:)`, `triggerHaptic(for:isHighPriority:)`

**Enums:**
- `SoundWorld` — System, Morgenwald, Salon, Cityflow, Horizont (5 Welten)
- `SoundAction` — 17 Aktionen, gruppiert: Tagbezug / Aufgabenbezug / Teil- und Hauptaufgabe / Settings
- `SoundVolume` — quiet, normal, loud

### MusicManager.swift

**MusicManager (`@Observable`)**
- `authorize()` — fragt MusicKit-Berechtigung an
- `loadInitialData()` — `checkSubscription` + `fetchPlaylists` + `fetchAlbums`
- `checkSubscription()` — Apple-Music-Abo-Status
- `togglePlayPause()`, `skipToNext()`, `skipToPrevious()`, `stop()` — Wiedergabe-Steuerung
- `play(playlist:)`, `play(album:)`, `play(track:in:)`, `playAll(_:)`
- `fetchPlaylists()`, `fetchAlbums()`, `fetchTracks(for:)`
- `syncPlaybackState()`, `syncCurrentTrack()` — State-Synchronisation

**Helper-Structs:** `PlaylistInfo`, `AlbumInfo`, `TrackInfo`

### UpdateChecker.swift

**UpdateChecker (`@Observable`)**
- `checkIfNeeded()` — App-Store-Check, max. 1×/24 h
- *(privat)* `fetchAppStoreVersion()` — iTunes Lookup
- *(privat)* `isVersion(_:newerThan:)` — semantischer Versionsvergleich

### DemoDataProvider.swift

**DemoDataProvider (struct)**
- `isActive` (static) — prüft Präsentationsmodus
- `activatingDeviceName` (static get/set), `isOnActivatingDevice` (static) — Aktivierungsgerät tracken
- `activate()`, `deactivate()` — Modus ein/aus, setzt `pendingResetKey`
- `loadPresentationDataIfNeeded(context:)` — lädt Bundle-JSON; bei aktivem Reset-Flag werden alle Demo-Daten zuerst gelöscht und dann frisch eingespielt

### CadenzaExportImport.swift

**CadenzaDataExporter (struct)**
- `exportData(context:appVersion:)` — Standard-Nutzer-Export
- `exportData(context:appVersion:datasetType:buildStage:buildStageName:description:)` — erweiterter Export mit Metadaten
- `countData(context:)` — Vorschau-Zähler

**CadenzaDataImporter (struct)**
- `preview(from:)` — Security-Scoped-Read, gibt `DataSummary` zurück
- `importData(from:into:mode:)` — File-Picker-Variante
- `importData(from:into:mode:referenceDate:resolveLocalizationKeys:)` — Kern: 4 Phasen (Delete, Kategorien, Aufgaben, Templates), 3 Zwischen-Saves für CloudKit-Strategie
- `deleteAllData(context:)` — Räumt Aufgaben, Kategorien, Templates

**Helper-Structs:** `CadenzaMetadata`, `CadenzaExport` (Format v2), `CategoryExport`, `MainTaskExport`, `TemplateExport`, `DataSummary`

### DateMigrator.swift

**DataMigrator (struct)**
- `migrateIfNeeded(context:)` — einmalige Migration deutsche → englische Enum-Rawvalues
- *(privat)* `migrateMainTasks(context:)`, `migrateSubTasks(context:)`

---

## 4. Datenmodell (SwiftData)

### MainTask

| Feld | Typ | Beschreibung |
|---|---|---|
| `title` | `String` | Aufgabentitel |
| `color` | `String` | Hex-Farbwert (Light-Mode, z. B. „#378ADD") |
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

**Methoden:** `syncStatus`, `applyStatusToSubTasks`, `applyExpiry`, `nextOccurrence(after:)`, `graceEndTime(gracePeriodHours:)`, `markDone(manually:)`, `markSkipped(manually:)`, `markOpen(manually:)` — Details siehe §3.

> Hinweis: Direktes Schreiben des `status`-Setters ist unzulässig — die Status-API in §6 ist die einzige zulässige Aufrufstelle. `lastExecutedDate` wird konsistent aus `setStatus(...)` UND `syncStatus()` gepflegt.

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

## 5. Design-System

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
| `DateGroupHeader` | TodayView „Ausblick" Datum-Gruppen |
| `UnifiedSubTaskBadge` | Teilaufgaben-Zähler (mit optionalem onToggle) |
| `WeekHeaderView` | HistoryView Kalenderwochen |
| `DayHeaderView` | HistoryView Tage |

### Klangwelten

5 Klangwelten: System, Morgenwald, Salon, Cityflow, Horizont. Alle 5 sind technisch implementiert.

> ⚠️ **Klangwelten-Redesign nach v1.7.2:** Klangwelten und Sound-Aktionen werden in einem vollständigen Redesign überarbeitet. Der hier dokumentierte Stand bildet die technische Basis ab, kann sich konzeptionell aber noch substantiell ändern.

### Navigations-Übersicht

| View | Titel-Stil | Besonderheit |
|---|---|---|
| TodayView | Custom Listenzeile (28pt bold) | + Datum-Zeile + Fortschrittsring |
| EditorView (AufgabenListeView) | Custom Listenzeile (28pt bold) | Toolbar: Kategorie-Manager, Template-Manager, Plus |
| HistoryView | Custom Listenzeile (28pt bold) | Kein Toolbar |
| SettingsView | Standard `.navigationTitle` | `.inline` Titel |

Alle drei Hauptviews verwenden `.navigationBarTitleDisplayMode(.inline)` + eigene Titel-Section für einheitliche Textausrichtung.

---

## 6. Architektur-Muster

### Drei-Container-Architektur

`AppMode` × `PersistenceController` halten drei vollständig getrennte SwiftData-Stores:

| Modus | Store | CloudKit | Zweck |
|---|---|---|---|
| `.production` | Default-Pfad | ✅ `iCloud.com.hoferer.cmt` | Echtdaten |
| `.presentation` | `Application Support/MyCadenza/presentation.store` | — | Demo & Präsentation (Endnutzer-Feature) |
| `.test` | `Application Support/MyCadenza/test.store` | — | Etappenspezifische Testszenarien (DEBUG-only) |

Der Modus wird in UserDefaults gespeichert. Der `ModelContainer` wird **einmal pro Prozess-Lifetime** gebaut — Moduswechsel erfordert App-Neustart. Foreground und Background-Task arbeiten am selben Container über `PersistenceController.shared`.

**Demo & Präsentation als Endnutzer-Feature** (Build 10718): Die Sektion „Demo & Präsentation" in `SettingsView` ist seit Build 10718 für alle Nutzer sichtbar. Aktivierung und Deaktivierung erfolgen über Confirmation-Dialoge mit Hilfetexten. Der `.test`-Modus bleibt DEBUG-only.

**Reset on Activation** (Build 10719): Bei jeder Aktivierung des Präsentationsmodus wird der Demo-Container vor dem Laden auf den Auslieferungszustand zurückgesetzt — Änderungen aus früheren Demo-Sitzungen gehen verloren. Mechanik: `DemoDataProvider.activate()` setzt `pendingResetKey` in UserDefaults; `loadPresentationDataIfNeeded(context:)` löscht beim nächsten Start alle Demo-Daten und lädt das Bundle frisch ein. Der Hilfetext im Aktivierungs-Dialog warnt explizit vor dem Verlust.

### Status-API auf MainTask

Direktes Schreiben des `status`-Setters lockt nicht und pflegt kein `lastExecutedDate`. Status-Änderungen laufen ausschließlich über:

- `markDone(manually:)` / `markSkipped(manually:)` — lockt per Default
- `markOpen(manually:)` — entsperrt grundsätzlich (Build 10712c), der Parameter ist wirkungslos
- `applyExpiry()` — Scheduler-Hook beim Ablauf der Verfallfrist
- `syncStatus()` — übernimmt den abgeleiteten Status nur, wenn `isManualStatus == false`

`applyStatusToSubTasks()` propagiert den Hauptstatus seit Build 10712c auch beim `.open`-Case symmetrisch auf die Teilaufgaben.

### Verfallfrist (Grace-Period)

Zwischen `MainTask.expiryTime` und `MainTask.graceEndTime(gracePeriodHours:)` ist die Aufgabe „überfällig aber offen" — der Nutzer kann sie noch erledigen. Erst nach Ablauf der Grace-Period setzt `TaskScheduler.processAllTasks` sie über `applyExpiry()` auf `.skipped` (mit Lock).

Die Frist liegt in UserDefaults unter `gracePeriodHours` (Default: 1.0), konfigurierbar in `SettingsView`.

### `@MainActor`-Konsistenz

Alle SwiftData-Operationen laufen auf dem MainActor, um Thread-Violations zu vermeiden:

- `TaskScheduler` ist `@MainActor`-isolierte Klasse (Build 10715, behebt TestFlight-Crash).
- `MainTask+Actions`-Extensions sind `@MainActor`-isoliert, weil sie auf `SoundManager.shared` und Scheduler zugreifen (Build 10716/10717).
- Der `BGTaskScheduler`-Closure läuft systemseitig auf einem Worker-Thread und hopt explizit per `Task { @MainActor in ... }` zurück, bevor `handleBackgroundTask` aufgerufen wird.
- Der `processAllTasks`-Debounce (30 s) und der `pendingRemoteChangeTask` (1,5 s Coalescing nach Remote-Sync-Bursts) sind durch die Klassen-Isolation race-frei.

### Konsolidierte Teilaufgaben- und Status-Operationen

`MainTask+Actions.swift` hält zwei Sektionen:

- **SubTask-Operations** (Build 10716): `cleanupCompletedSubTasks(context:)` und `resetAllSubTasks(context:)` sind die einzige zulässige Aufrufstelle für Snapshot + Cleanup/Reset. Vor Build 10716 war diese Logik dreifach in `EditTaskView`, `TodayTaskRowView` und `SubTaskSheetView` dupliziert — mit subtilen Inkonsistenzen.
- **Status-Aktionen** (Build 10717): `performMarkOpen()`, `performMarkDone(context:)`, `performMarkSkipped(context:)` bündeln Status-Wechsel + Sound + Scheduler-Anbindung. Views rufen ausschließlich diese Methoden auf, nicht mehr `markX` direkt mit anschließendem manuellem Sound-/Scheduler-Aufruf.

### Localization

- Alle UI-Strings: `String(localized: "Deutscher Key")`
- Keys sind **deutsch mit korrekten Umlauten** (ä, ö, ü, ß)
- Übersetzungen in `Localizable.xcstrings`
- ⚠️ Schutzwörter bei Umlaut-Operationen: „Neue", „zuerst" enthalten „ue", das KEIN Umlaut ist

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
- **Automatisch nach Versionsupdate:** `WhatsNewView` als Sheet, gesteuert über `@AppStorage("lastSeenWhatsNew")` (Versions-String-Vergleich) — neue Nutzer (frischer Onboarding-Lauf) sehen das Sheet nicht, weil `lastSeenWhatsNew` direkt auf die aktuelle Version gesetzt wird
- **Manuell aus Settings** (Build 10718): Im Über-Block ein „Was ist neu"-Button, der dasselbe Sheet öffnet, ohne `lastSeenWhatsNew` zu manipulieren
- Im DEBUG-Build: Reset-Button für `lastSeenWhatsNew` in der Entwicklung-Sektion

### Update-Checker

- `UpdateChecker` (`@Observable`) ruft beim App-Start und bei jedem `.active`-scenePhase-Wechsel die iTunes-Lookup-API auf
- Throttle: max. 1 Check pro Tag (UserDefaults-Datum)
- Bei verfügbarer neuerer Version → `.alert` in `ContentView` mit „Aktualisieren"-Button (öffnet App Store)

### Sound-System

`SoundManager` definiert **5 Klangwelten** und **17 Aktionen**:

**Klangwelten:** System, Morgenwald, Salon, Cityflow, Horizont. WAVs liegen unter `Sounds/{klangwelt}/{klangwelt}_{aktion}.wav`. Bei fehlender WAV greift eine Fallback-Mechanik (Mapping auf eine semantisch nahestehende Aktion). Klangwelten-Redesign ist nach v1.7.2 geplant.

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

**Sound-Dauern (Richtwerte für ElevenLabs-Generierung):**

| Aktionsgruppe | Zieldauer |
|---|---|
| Teilaufgabe (erledigt/übersprungen) | 0,5 s |
| Hauptaufgabe (erledigt/übersprungen/überfällig erledigt) | 1,5 s |
| Tagbezug, Verfall, Cleanup/Reset | 2,0 s |
| Tag komplett | 2,5 s |

Klangwelt-Zuweisung erfolgt über die Kategorie (`TaskCategory.soundWorldRaw`). Tag-Beginn/Ende hat eine eigene globale Einstellung (`dayBookendSoundWorld`). Audio Session: `.ambient` (respektiert Silent-Switch). Aufgaben mit hoher Priorität werden lauter gespielt (Faktor 1.0 vs. 0.85). System-Sound-IDs sind fixiert: 1001 / 1054 / 1307.

### CloudKit-Sync

- Container: `iCloud.com.hoferer.cmt` (nur in `.production`)
- Sync via SwiftData `.cloudKitDatabase(.private(...))`
- Remote-Change-Listener: `NSPersistentStoreRemoteChange` → `TaskScheduler.scheduleRemoteChangeProcessing`
- Coalescing: Bursts werden zu einem einzigen Scan gebündelt (1,5 s nach letzter Notification)
- Duplikat-Bereinigung in `TaskScheduler.cleanupDuplicates()` zu Beginn jedes `processAllTasks`-Laufs
- Replace-Import legt CloudKit-Zwischen-Saves an, damit der Import bei großen Datenmengen nicht abreißt

---

## 7. Bekannte Fallstricke

| Problem | Lösung |
|---|---|
| iCloud Container `iCloud.com.hoferer.ChrisMeinTag` defekt | Immer `iCloud.com.hoferer.cmt` verwenden |
| `.swipeActions` + `.editMode(.constant(.active))` inkompatibel | Context Menus + Trash Icons |
| `String(localized:)` liest Bundle bei Launch, nicht bei `.environment(\.locale)` | Per-App Language via iOS Settings |
| `.navigationTitle` im Large-Title-Modus nicht an Listen-Content ausgerichtet | Custom Titel als erste Listenzeile |
| SwiftUI Button in Section Header wird blau gefärbt | `.buttonStyle(.plain)` |
| Spacer() in Buttons nicht tappbar | `.contentShape(Rectangle())` auf den HStack |
| `doesNotExpire` Tasks mit Zukunfts-Startdatum | Gehören in „Ausblick", nicht ausfiltern |
| Wörter mit „ue" die kein Umlaut sind | „Neue", „zuerst" schützen bei Batch-Umlaut-Operationen |
| Bundle Sounds nicht gefunden | Root-Level oder Klangwelt-Unterordner, kein `subdirectory` Parameter |
| App Store Version Train geschlossen | Version bump (z. B. 1.5.0 → 1.5.1) bei neuem Build nötig |
| `ModelContainer` wird nur 1× pro Prozess gebaut | Moduswechsel (AppMode) erfordert App-Neustart |
| SwiftData aus `BGTaskScheduler`-Closure crasht (Thread-Violation) | Explizit `Task { @MainActor in ... }` vor SwiftData-Zugriff |
| Direktes `task.status = .done` lockt nicht und pflegt kein `lastExecutedDate` | `markDone/markSkipped/markOpen` verwenden |
| SubTask-Cleanup-Logik inline in Views weicht voneinander ab | Nur `MainTask.cleanupCompletedSubTasks` / `resetAllSubTasks` aufrufen |
| Status-Wechsel inline in Views ohne Sound/Scheduler | Nur `MainTask.performMarkOpen/Done/Skipped` aufrufen |
| MusicKit funktioniert nicht im Simulator | Auf echtem Gerät testen |
| `@StateObject` mit `.shared`-Singleton bricht Subscription-Propagation in TabView-Subtrees (entdeckt in 10722) | Singletons direkt via `.environmentObject(.shared)` injizieren — kein `@StateObject`-Wrapper |
| `@State` + `.repeatForever()`-Animation bleibt bei Status-Wechsel hängen, weil der Animation-Cycle noch läuft (entdeckt in 10722) | `TimelineView(.animation)` für zeit-getriebene Pulsation; per `if condition { PulsingView } else { StaticView }` Subview-Austausch erzwingen, statt `@State`-Toggle |

---

## 8. DEBUG-Helfer

Im DEBUG-Build sichtbare Hilfen, gebündelt in der „Entwicklung"-Sektion der `SettingsView`. Im Production-Build vollständig unsichtbar (`#if DEBUG`).

- **Sound-Test** — `SoundTestView` als Testbench für alle `SoundAction × SoundWorld`-Kombinationen
- **WhatsNew-Flag zurücksetzen** — UserDefaults-Reset für `lastSeenWhatsNew`, damit das Sheet beim nächsten App-Start automatisch erneut erscheint
- **Modus-Wechsel inkl. `.test`** — Wechsel zwischen `production`, `presentation` und `test`; im Release-Build steht nur `production` und `presentation` zur Verfügung
- **Sofortverfall-Helfer** — geplant im „Feinere Zeitstufen"-Konzept (siehe TodoList; noch nicht implementiert)

---

## 9. Aktueller Stand

- **App Store:** v1.6.1 (Build 10602)
- **Bei Apple zur Prüfung:** v1.7.0 (Build 10722)

### Implementiert in v1.7.0

- ✅ 4 Tab-Ansichten: Heute, Aufgaben, Historie, Einstellungen
- ✅ Aufgaben mit Teilaufgaben, Kategorien, Wiederholungen, Prioritäten
- ✅ Einheitliche aufklappbare Listen (Chevron + Tap Muster)
- ✅ „Ausblick" als vollwertige Sektion mit Datum-Gruppen
- ✅ Onboarding (4 Screens beim ersten Start)
- ✅ „Was ist neu"-Sheet — automatisch nach Versionsupdate + manuell aus dem Über-Block (Build 10718)
- ✅ App-Store-Update-Hinweis (`UpdateChecker`)
- ✅ Verfallfrist (Grace-Period) zwischen Verfall und automatischem Skipping
- ✅ Status-API auf `MainTask` mit Lock-Semantik (`markDone/markSkipped/markOpen`, `applyExpiry`)
- ✅ Drei-Container-Architektur (Produktion / Präsentation / Test)
- ✅ **Demo & Präsentation als reguläres Endnutzer-Feature** (Build 10718) inkl. Reset on Activation (Build 10719)
- ✅ `@MainActor`-konsistenter Scheduler — TestFlight-Crash gefixt
- ✅ Konsolidierte SubTask-Operations und Status-Aktionen auf `MainTask` (`MainTask+Actions.swift`)
- ✅ Klang-System: 5 Klangwelten × 17 Aktionen (Cityflow + Horizont implementiert) — Redesign nach v1.7.2 geplant
- ✅ MusicKit-Integration (Apple Music, AirPlay/Bluetooth-Picker, Player-Leiste in TodayView)
- ✅ Export/Import (JSON) mit CloudKit-Zwischen-Saves beim Replace-Import
- ✅ Localization DE + EN; Lücke „Praesentationsmodus" → „Präsentationsmodus" gefixt (Build 10718)
- ✅ Alle Umlaute korrekt (ä, ö, ü, ß) in Code und xcstrings
- ✅ CloudKit-Sync mit Remote-Change-Coalescing
- ✅ SoundTestView als DEBUG-Testbench für alle Aktionen × Klangwelten
- ✅ Farbpalette mit 9×5 kuratierten Farben + Light/Dark
- ✅ Datenschutzrichtlinie + Support-Kontakt verlinkt (live seit 21.04.2026)
- ✅ Tote-Code-Bereinigung in Views und xcstrings (Build 10720)
- ✅ Localization-Cleanup: stale-keys entfernt, dynamische Keys auf `String(format:)` migriert, „Praesentation" → „Präsentation" (Build 10721)
- ✅ Latentbug seit v1.6.1 in Cleanup-Bestätigung beim selben Build mit-eliminiert
- ✅ CloudKit-Status-System mit fünf Zuständen (`initialContact`/`syncing`/`synced`/`failed`/`localOnly`), Header-Punkt mit konditionalem Mini-Text, eigene Sektion in den Einstellungen (Build 10722)
- ✅ Header-Refactor in TodayView: ProgressRing 32×32 pt links neben „Heute", iCloud-Status rechts, immer sichtbar (Build 10722)
- ✅ Wiederhersteller-Banner aus TodayView entfernt — Status-Bereich übernimmt diese Information bei `initialContact` mit leerer DB (Build 10722)
- ✅ Import-/Export-Sperre während aktivem iCloud-Sync, mit Section-Footer-Hinweis (B-M2, letzter v1.7.0-Release-Blocker, Build 10722)
- ✅ Tagesbericht-Modal mit kontext-sensitivem Status-Hinweis-Block (Build 10722)

### Geplant

Detaillierter Sprint-Plan und Befund-IDs:

- **Single Source of Truth (Sprint):** `MyCadenza_TodoList_v1_7.md`
- **Single Source of Truth (Befunde):** `MyCadenza_CodeReview_v1_7_0.md`

Offene größere Themen außerhalb der laufenden Sprint-Etappen:

- ⬜ v1.7.0-Release-Blocker B-M2 (Import-Sperre bei aktivem CloudKit-Sync)
- ⬜ Klangwelten-Redesign (nach v1.7.2)
- ⬜ `defaultValidityDuration`/`defaultRepeatInterval` AppStorage in `AddTaskView` ankoppeln
- ⬜ In-App Sprachauswahl (statt iOS Settings Redirect)
- ⬜ Tägliche Zusammenfassung als Push-Notification
- ⬜ Algorithmische Tagesmelodie (musikalisches Abbild des Tagesfortschritts)
- ⬜ Kontextuelle Hilfetexte in Views (Schicht 2 der Anwenderdoku)
- ⬜ Quick Start + Vollständige Doku auf GitHub Pages (Schicht 3+4)

---

## 10. Session-Checkliste für Claude

Bei jeder neuen Session:

1. **Diese Datei lesen** für Projektüberblick
2. **§3 Klassen- und Methoden-Referenz** als erste Anlaufstelle bei Datei-/Methoden-Fragen — Datei-Anforderungen präzise mit Klassennamen + Methodennamen
3. **`MyCadenza_TodoList_v1_7.md` und `MyCadenza_CodeReview_v1_7_0.md` als Single Source of Truth** für aktuellen Sprint und Befund-Status konsultieren
4. **Betroffene Swift-Dateien anfordern** — nicht aus dem Gedächtnis arbeiten
5. **Komplette Dateien liefern** — Chris bevorzugt Full-File-Replacement statt Diffs (bei großen Dateien gezielte Edits)
6. **Umlaut-Regeln beachten:** alle `String(localized:)` Keys mit korrekten Umlauten (ä, ö, ü, ß), aber „Neue" und „zuerst" schützen
7. **Iterativ arbeiten:** Ein Schritt → Testen → Nächster Schritt (Build-Stage-Methodik)
8. **Clean Build empfehlen** bei Localization-Änderungen (⌘⇧K, dann ⌘B)
9. **Status-Änderungen am `MainTask`** ausschließlich über `markDone/markSkipped/markOpen` — nie direktes `task.status = ...`
10. **SubTask-Cleanup/Reset und Status-Aktionen aus Views** ausschließlich über `MainTask.cleanupCompletedSubTasks` / `resetAllSubTasks` / `performMarkOpen` / `performMarkDone(context:)` / `performMarkSkipped(context:)`
