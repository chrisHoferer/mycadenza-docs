# MyCadenza – Klangwelt-Dateischema & WAV-Inventar

> Referenz-Dokument für den KlangweltReview und WAV-Pflege.
> Stand: 04.05.2026
> Inventar gegen App-Repo Sounds-Ordner abgeglichen am 04.05.2026.

---

## Schema

**Dateinamen-Pattern:** `{klangwelt}_{aktion}.wav`

Beispiele:
- `morgenwald_tagBegonnen.wav`
- `salon_aufgabeFaellig.wav`
- `cityflow_teilaufgabeErledigt.wav`
- `horizont_hauptaufgabeKomplett.wav`

Beide Bestandteile entsprechen den `rawValue`-Strings der Swift-Enums `SoundWorld` bzw. `SoundAction` in `SoundManager.swift`. Casing ist signifikant — Swift `Bundle.main.url(forResource:withExtension:)` ist case-sensitive.

**Hinweis zur Xcode-Integration:** Der `Sounds/`-Ordner ist im Xcode-Projekt als Folder-Reference (blaue Mappe) eingebunden, nicht als Group. Beim Build kopiert Xcode alle WAVs flach ins App-Bundle — neue Dateien in `Sounds/<Klangwelt>/` werden automatisch erfasst, ein manueller pbxproj-Edit ist nicht nötig. Subordner-Pfade spielen zur Laufzeit keine Rolle, weil `Bundle.main.url(forResource:withExtension:)` ohne `subdirectory:`-Parameter aufgerufen wird.

## Sucht-Hierarchie zur Laufzeit

Wenn ein Sound für eine Aktion in einer Klangwelt abgespielt werden soll, durchläuft der `SoundManager` drei Stufen in dieser Reihenfolge:

1. **Eigene WAV** — `{klangwelt}_{aktion}.wav` aus dem App-Bundle
2. **Fallback-WAV** — die für diese Aktion definierte Ersatz-Aktion in derselben Klangwelt: `{klangwelt}_{fallback-aktion}.wav`
3. **System-Sound-ID** — `AudioServicesPlayAlertSound(action.systemSoundID)`

Die System-Klangwelt nutzt grundsätzlich Stufe 3 direkt — sie hat heute keine eigenen WAVs. Eine Umstellung auf eigene WAVs ist im Backlog (TodoList: „System-Klangwelt vollständig mit eigenen WAVs").

## Klangwelten

| RawValue | Anzeige | Icon | WAV-Set |
|---|---|---|---|
| `system` | System | `iphone.gen3` | – (nutzt System-Sound-IDs direkt) |
| `morgenwald` | Morgenwald | `leaf.fill` | (Inventar siehe unten) |
| `salon` | Salon | `pianokeys` | (Inventar siehe unten) |
| `cityflow` | Cityflow | `building.2.fill` | (Inventar siehe unten) |
| `horizont` | Horizont | `sparkles` | (Inventar siehe unten) |

## Aktionen

Sechs Stamm-Aktionen ohne Fallback (müssen pro WAV-Klangwelt zwingend vorhanden sein):
`tagBegonnen`, `tagKomplett`, `tagBeendet`, `teilaufgabeErledigt`, `teilaufgabeUebersprungen`, `hauptaufgabeKomplett`.

Elf Aktionen mit Fallback (können fehlen — Sucht-Hierarchie greift):

| Gruppe | RawValue | Anzeige | Fallback | System-ID |
|---|---|---|---|---|
| Tag | `tagBegonnen` | Tag begonnen | – (Stamm) | 1001 |
| Tag | `tagKomplett` | Tag komplett | – (Stamm) | 1307 |
| Tag | `tagBeendet` | Tag beendet | – (Stamm) | 1104 |
| Aufgabe | `aufgabeFaellig` | Aufgabe fällig | `tagBegonnen` | 1307 |
| Aufgabe | `aufgabeUeberfaellig` | Aufgabe überfällig | `aufgabeFaellig` | 1103 |
| Aufgabe | `aufgabeVerfallen` | Aufgabe verfallen | `teilaufgabeUebersprungen` | 1104 |
| Settings | `exportImport` | Export / Import | `hauptaufgabeKomplett` | 1307 |
| Administration | `aufgabeErstellt` | Aufgabe erstellt | `teilaufgabeErledigt` | 1001 |
| Administration | `aufgabeGeloescht` | Aufgabe gelöscht | `teilaufgabeUebersprungen` | 1103 |
| Administration | `erledigteEntfernt` | Erledigte entfernt | `teilaufgabeUebersprungen` | 1054 |
| Administration | `aufgabeZurueckgesetzt` | Aufgabe zurückgesetzt | `teilaufgabeUebersprungen` | 1001 |
| Prozess | `teilaufgabeErledigt` | Teilaufgabe erledigt | – (Stamm) | 1054 |
| Prozess | `teilaufgabeUebersprungen` | Teilaufgabe übersprungen | – (Stamm) | 1057 |
| Prozess | `hauptaufgabeErledigt` | Hauptaufgabe erledigt | `hauptaufgabeKomplett` | 1307 |
| Prozess | `hauptaufgabeUebersprungen` | Hauptaufgabe übersprungen | `teilaufgabeUebersprungen` | 1104 |
| Prozess | `hauptaufgabeKomplett` | Hauptaufgabe komplett | – (Stamm) | 1307 |
| Prozess | `ueberfaelligErledigt` | Überfällig erledigt | `hauptaufgabeKomplett` | 1307 |

## Erwartetes vollständiges Dateinamen-Set

**Pro WAV-Klangwelt: 17 Dateien.**
**Vier aktive WAV-Klangwelten (ohne System): 68 Dateien.**
**Bei späterer Aufnahme der System-Klangwelt mit eigenen WAVs: 85 Dateien.**

Vollständige Liste pro Klangwelt:

### Morgenwald
- `morgenwald_tagBegonnen.wav`
- `morgenwald_tagKomplett.wav`
- `morgenwald_tagBeendet.wav`
- `morgenwald_aufgabeFaellig.wav`
- `morgenwald_aufgabeUeberfaellig.wav`
- `morgenwald_aufgabeVerfallen.wav`
- `morgenwald_exportImport.wav`
- `morgenwald_aufgabeErstellt.wav`
- `morgenwald_aufgabeGeloescht.wav`
- `morgenwald_erledigteEntfernt.wav`
- `morgenwald_aufgabeZurueckgesetzt.wav`
- `morgenwald_teilaufgabeErledigt.wav`
- `morgenwald_teilaufgabeUebersprungen.wav`
- `morgenwald_hauptaufgabeErledigt.wav`
- `morgenwald_hauptaufgabeUebersprungen.wav`
- `morgenwald_hauptaufgabeKomplett.wav`
- `morgenwald_ueberfaelligErledigt.wav`

### Salon
(analog mit Präfix `salon_`)

### Cityflow
(analog mit Präfix `cityflow_`)

### Horizont
(analog mit Präfix `horizont_`)

## Inventar (Stand 04.05.2026)

Abgleich der erwarteten Dateinamen gegen den tatsächlichen Bestand im App-Repo (`Sounds/`-Ordner). ✓ = vorhanden, ❌ = fehlt.

| Aktion | Morgenwald | Salon | Cityflow | Horizont |
|---|---|---|---|---|
| `tagBegonnen` | ✓ | ✓ | ✓ | ✓ |
| `tagKomplett` | ✓ | ✓ | ✓ | ✓ |
| `tagBeendet` | ✓ | ✓ | ✓ | ✓ |
| `aufgabeFaellig` | ✓ | ✓ | ✓ | ✓ |
| `aufgabeUeberfaellig` | ❌ | ❌ | ❌ | ❌ |
| `aufgabeVerfallen` | ✓ | ✓ | ✓ | ✓ |
| `exportImport` | ✓ | ✓ | ✓ | ✓ |
| `aufgabeErstellt` | ✓ | ✓ | ✓ | ✓ |
| `aufgabeGeloescht` | ✓ | ✓ | ✓ | ✓ |
| `erledigteEntfernt` | ✓ | ✓ | ✓ | ✓ |
| `aufgabeZurueckgesetzt` | ✓ | ✓ | ✓ | ✓ |
| `teilaufgabeErledigt` | ✓ | ✓ | ✓ | ✓ |
| `teilaufgabeUebersprungen` | ✓ | ✓ | ✓ | ✓ |
| `hauptaufgabeErledigt` | ❌ | ❌ | ❌ | ❌ |
| `hauptaufgabeUebersprungen` | ❌ | ❌ | ❌ | ❌ |
| `hauptaufgabeKomplett` | ✓ | ✓ | ✓ | ✓ |
| `ueberfaelligErledigt` | ✓ | ✓ | ✓ | ✓ |

**Zusammenfassung:**
- Morgenwald: 14 von 17 vorhanden
- Salon: 14 von 17 vorhanden
- Cityflow: 14 von 17 vorhanden
- Horizont: 14 von 17 vorhanden
- Gesamt: 56 von 68 vorhanden (3 Aktionen fehlen in allen vier Klangwelten)

## Auffälligkeiten

### Drei Aktionen fehlen in allen WAV-Klangwelten
- `aufgabeUeberfaellig` — fällt zur Laufzeit auf `aufgabeFaellig` zurück
- `hauptaufgabeErledigt` — fällt auf `hauptaufgabeKomplett` zurück
- `hauptaufgabeUebersprungen` — fällt auf `teilaufgabeUebersprungen` zurück

Funktional unauffällig durch die Sucht-Hierarchie, klingt aber nicht differenziert. Generierung der zwölf fehlenden WAVs (3 × 4 Klangwelten) ist Teil des Klangwelt-Volltests.

### Hygiene-Stand hergestellt am 04.05.2026
- Vorher: Cityflow hatte 5 Dateien mit Leerzeichen im Namen (`cityflow <aktion>.wav`), die zur Laufzeit nicht gefunden wurden, weil das Sucht-Pattern Unterstrich verlangt.
- Vorher: 4 tote `*uiAktion.wav`-Dateien (eine pro WAV-Klangwelt) zu einer Aktion, die im Code nicht mehr existiert.
- Vorher: Subordner-Casing inkonsistent (`cityflow/`, `morgenwald/`, `salon/` klein, `Horizont/` groß) — funktional irrelevant wegen Folder-Reference (siehe „Schema"-Sektion), aber visuell unsauber.
- Bereinigung am 04.05.2026 in Xcode; Hygiene-Commit `59aac74` im App-Repo, kein Buildnummer-Bump, kein Tag.

## Verweise

- Code-Definition: `SoundManager.swift` — `enum SoundWorld` (Z. 9-37), `enum SoundAction` (Z. 42-153)
- Sucht-Logik: `SoundManager.swift` — `playBundleSound(action:world:isHighPriority:)` (etwa Z. 419-441)
- ElevenLabs-Prompts: `Cadenza_ElevenLabs_Prompts.md`
- Backlog WAV-Review: `MyCadenza_TodoList_v1_7.md` (WAVs & Sound-Sektion)
