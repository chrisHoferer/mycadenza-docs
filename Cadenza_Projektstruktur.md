# MyCadenza – Projektstruktur & Referenz

> Dieses Dokument dient als Claude-Referenz für neue Sessions.
> Stand: 30.03.2026 · v1.5.2 (Build 10506)

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
| Framework | SwiftUI + SwiftData + CloudKit |
| Sprachen | Deutsch (Quelle), Englisch |
| Localization | `String(localized:)` mit deutschen Keys in `Localizable.xcstrings` (252 Keys) |

---

## 2. Dateiverzeichnis

### Views (Hauptansichten)

| Datei | Enthält | Beschreibung |
|---|---|---|
| `ContentView.swift` | `ContentView`, `SplashView` | TabView (4 Tabs: Heute, Aufgaben, Historie, Einstellungen) + Splash Screen + Onboarding-Trigger |
| `OnboardingView.swift` | `OnboardingView` | 4-Screen PageView beim ersten Start (AppStorage-gesteuert) |
| `TodayView.swift` | `TodayView`, `TodayTaskRowView`, `ProgressRingView`, `SubTaskProgressRing`, `TaskSnapshotHelper`, `InlineSubTaskRow`, `SubTaskSheetView`, `SubTaskRowView` | Tagesansicht mit 5 Sektionen (Überfällig, Fällig, Anstehend, Ausblick, Abgeschlossen) |
| `EditorView.swift` | `EditorView`, `AufgabenListeView`, `TaskEditorRowView`, `AddTaskView`, `EditTaskView`, `AddSubTaskView`, `EditSubTaskView` | Aufgabenverwaltung, Erstellen/Bearbeiten von Aufgaben |
| `HistoryView.swift` | `HistoryView`, `HistoryTaskRowView` | Wochen/Tage/Kategorie-Hierarchie, read-only Ansicht erledigter Aufgaben |
| `SettingsView.swift` | `SettingsView`, `DailySummaryScheduler`, `CadenzaExportDocument` | Einstellungen, Export/Import, Klang, Darstellung, Präsentationsmodus |
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
| `Models.swift` | `RepeatInterval` (enum), `CustomIntervalUnit` (enum), `TaskStatus` (enum), `SubTask` (@Model), `MainTask` (@Model) | Kern-Datenmodell: Aufgaben + Teilaufgaben + Enums |
| `TaskCategory.swift` | `TaskCategory` (@Model) | Kategorien mit Farbe, Icon, Klangwelt |
| `TaskTemplate.swift` | `TemplateSubTask` (@Model), `TaskTemplate` (@Model) | Vorlagen für wiederkehrende Aufgaben |

### Logik & Services

| Datei | Enthält | Beschreibung |
|---|---|---|
| `CadenzaApp.swift` | `CadenzaApp` (@main) | App-Einstiegspunkt, ModelContainer, CloudKit-Sync, Background Tasks |
| `TaskScheduler.swift` | `TaskScheduler` | Aufgabenverarbeitung, Wiederholungen, Erinnerungen, Badge, Background Refresh, Duplikat-Bereinigung |
| `SoundManager.swift` | `SoundWorld` (enum), `SoundAction` (enum), `SoundVolume` (enum), `SoundManager` (singleton) | Klang-System: 4 Klangwelten, 5 Aktionen, AVAudioPlayer, Haptik |
| `AppPalette.swift` | `AppPalette`, `PaletteStop`, `PaletteFamily`, `Color` Extension | Farbsystem: 9 Familien × 5 Stufen, Gold-Chrome, Light/Dark-Auflösung, Hex-Konvertierung |
| `CadenzaExportImport.swift` | `CadenzaExport`, Export-Structs, `CadenzaDataExporter`, `CadenzaDataImporter`, `ImportError` | JSON-Export/Import des gesamten Datenbestands |
| `DateMigrator.swift` | `DataMigrator` | Einmalige Migration: deutsche Enum-Rawvalues → englische (v1-Kompatibilität) |
| `DemoDataProvider.swift` | `DemoDataProvider` | Präsentationsmodus: Demo-Daten laden/wiederherstellen, Backup-System |

### Ressourcen

| Pfad | Beschreibung |
|---|---|
| `Sounds/` | WAV-Dateien (48kHz): `{klangwelt}_{aktion}.wav`, z.B. `morgenwald_teilaufgabeErledigt.wav` |
| `Assets.xcassets` | App Icon (1024×1024), SplashLogo, LaunchBackground (#C8973A) |
| `Localizable.xcstrings` | Localization: Deutsche Keys → Englische Übersetzungen (252 Keys) |
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
| `isManualStatus` | `Bool` | Manuell gesetzt vs. automatisch abgeleitet |
| `lastExecutedDate` | `Date?` | Letzte Ausführung |
| `isActive` | `Bool` | Aktiv/Inaktiv |
| `repeatGroupID` | `String` | UUID, verknüpft Wiederholungen einer Aufgabe |
| `customIntervalValue` | `Int` | Benutzerdefiniertes Intervall (Wert) |
| `customIntervalUnitRaw` | `String` | Benutzerdefiniertes Intervall (Einheit: days/workdays/weeks/months) |
| `sortOrder` | `Int` | Sortierung innerhalb der Kategorie |
| `hasReminder` | `Bool` | Erinnerung zur Startzeit |
| `isHighPriority` | `Bool` | Hohe Priorität (rotes Dreieck) |
| `doesNotExpire` | `Bool` | Kein Verfall (nur bei .once) |
| `subTasks` | `[SubTask]?` | Teilaufgaben (cascade delete) |
| `category` | `TaskCategory?` | Zugewiesene Kategorie |

Berechnete Properties: `expiryTime`, `repeatInterval`, `customIntervalUnit`, `status`, `derivedStatus`
Methoden: `syncStatus()`, `applyStatusToSubTasks()`, `applyExpiry()`, `nextOccurrence(after:)`

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
| `soundWorldRaw` | `String` | Klangwelt (morgenwald/salon) |
| `tasks` | `[MainTask]?` | Verknüpfte Aufgaben (inverse) |
| `templates` | `[TaskTemplate]?` | Verknüpfte Templates (inverse) |

### TaskTemplate / TemplateSubTask

Gleiche Struktur wie MainTask/SubTask, aber ohne `status`, `isActive`, `lastExecutedDate`.

### Beziehungen

```
TaskCategory 1 ←→ N MainTask
TaskCategory 1 ←→ N TaskTemplate
MainTask     1 ←→ N SubTask        (cascade delete)
TaskTemplate 1 ←→ N TemplateSubTask (cascade delete)
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
- Keine `+/−/++/−−` Buttons mehr (entfernt in v1.5.2)

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

### Localization

- Alle UI-Strings: `String(localized: "Deutscher Key")`
- Keys sind **deutsch mit korrekten Umlauten** (ä, ö, ü, ß)
- Übersetzungen in `Localizable.xcstrings` (252 Keys, alle mit EN-Übersetzung)
- ⚠️ Schutzwörter bei Umlaut-Operationen: "Neue", "zuerst" enthalten "ue" das KEIN Umlaut ist

### Datumsformatierung

- Locale-adaptive Formate: `formatter.setLocalizedDateFormatFromTemplate("EEEEdMMMM")`
- Nicht: `formatter.dateFormat = "EEEE, d. MMMM"` (wäre deutsches Hardcoding)

### Listen mit Edit-Mode

- `.environment(\.editMode, .constant(.active))` für Drag-to-Reorder
- `.swipeActions` funktioniert NICHT mit editMode → Context Menus + Trash Icons verwenden
- Navigation: `Button + .navigationDestination(item:)` statt `NavigationLink`

### Section-Hierarchie

Kategorien werden als echte `Section { } header:` verwendet → grauer Hintergrund.
Übergeordnete Header (Fällig, KW) werden als leere `Section { } header:` eingesetzt.
`.listSectionSpacing(.compact)` auf allen Listen für enge Abstände.

### Onboarding

- 4-Screen PageView beim ersten Start (`OnboardingView.swift`)
- Gesteuert über `@AppStorage("hasSeenOnboarding")`
- Wird als `.fullScreenCover` nach dem Splash-Screen angezeigt
- Gold-Chrome-Design passend zur App-Ästhetik

### Sound-System (Klangkonzept)

- 5 Aktionen: tagBegonnen, teilaufgabeErledigt, teilaufgabeUebersprungen, hauptaufgabeKomplett, tagBeendet
- 4 Klangwelten: Morgenwald (Natur), Salon (Klavier), Cityflow (Lo-Fi), Horizont (Ambient)
- Klangwelt-Zuweisung über Kategorie (`TaskCategory.soundWorldRaw`)
- Tag-Beginn/Ende: eigene globale Einstellung (`dayBookendSoundWorld`)
- Bundle: `{klangwelt}_{aktion}.wav` im Root (kein subdirectory)
- Audio: `.ambient` Session, respektiert Silent-Switch

### CloudKit-Sync

- Container: `iCloud.com.hoferer.cmt`
- Sync via SwiftData `.cloudKitDatabase(.private(...))`
- Remote-Change-Listener: `NSPersistentStoreRemoteChange` → `processPendingChanges()`
- Duplikat-Bereinigung in `TaskScheduler.cleanupDuplicates()` nach jedem Sync

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
| Bundle Sounds nicht gefunden | Root-Level, kein `subdirectory` Parameter |
| App Store Version Train geschlossen | Version bump (z.B. 1.5.0 → 1.5.1) bei neuem Build nötig |

---

## 7. Aktueller Stand (v1.5.2, Build 10506)

### Implementiert

- ✅ 4 Tab-Ansichten: Heute, Aufgaben, Historie, Einstellungen
- ✅ Aufgaben mit Teilaufgaben, Kategorien, Wiederholungen, Prioritäten
- ✅ Einheitliche aufklappbare Listen (Chevron + Tap Muster)
- ✅ "Ausblick" als vollwertige Sektion mit Datum-Gruppen
- ✅ Onboarding (4 Screens beim ersten Start)
- ✅ Klang-System: Morgenwald + Salon implementiert
- ✅ Export/Import (JSON)
- ✅ Localization DE + EN (252 Keys, alle mit Übersetzung)
- ✅ Alle Umlaute korrekt (ä, ö, ü, ß) in Code und xcstrings
- ✅ CloudKit Sync
- ✅ Präsentationsmodus (Demo-Daten)
- ✅ Farbpalette mit 9×5 kuratierten Farben + Light/Dark
- ✅ App-Name: MyCadenza (DE + EN, Code + App Store Connect)

### Geplant

- ⬜ Cityflow + Horizont Klangwelten generieren (ElevenLabs)
- ⬜ `defaultValidityDuration`/`defaultRepeatInterval` AppStorage mit AddTaskView verbinden
- ⬜ In-App Sprachauswahl (statt iOS Settings Redirect)
- ⬜ Privacy Policy URL + Support Contact
- ⬜ Git Workflow Erklärung für Chris
- ⬜ Kontextuelle Hilfetexte in Views (Schicht 2 der Anwenderdoku)
- ⬜ Quick Start + Vollständige Doku auf GitHub Pages (Schicht 3+4)

---

## 8. Session-Checkliste für Claude

Bei jeder neuen Session:

1. **Diese Datei lesen** für Projektüberblick
2. **Betroffene Swift-Dateien anfordern** — nicht aus dem Gedächtnis arbeiten
3. **Komplette Dateien liefern** — Chris bevorzugt Full-File-Replacement statt Diffs
4. **Umlaut-Regeln beachten**: Alle `String(localized:)` Keys mit korrekten Umlauten (ä, ö, ü, ß), aber "Neue" und "zuerst" schützen
5. **Iterativ arbeiten**: Ein Schritt → Testen → Nächster Schritt
6. **Clean Build empfehlen** bei Localization-Änderungen (⌘⇧K, dann ⌘B)
