# MyCadenza – Zielarchitektur

> Entwicklungs- und Architekturplan für MyCadenza.
> Stand: 25.04.2026 · v1.6.1 im App Store · Build 10716 in Entwicklung gepusht · TestFlight-BG-Crash gelöst (10715), SubTask-Operations konsolidiert (10716)
> Zweck: Gemeinsamer Bezugsrahmen für Architekturentscheidungen und Feature-Priorisierung.
> Lebendes Dokument — wächst und korrigiert sich im Laufe der Umsetzung.

> **Begleitdokumente:**
> - `MyCadenza_CodeReview_v1.7.0.md` — aktueller Code-Review bezieht sich auf diesen Plan
> - `MyCadenza_TodoList_v1_7.md` — konkrete Etappen-Planung
> - `Kanzlei_Kommunikation_Konzeptskizze.md` — paralleles Konzeptthema, separat gehalten

---

## 1. Vision

MyCadenza ist und bleibt ein **persönlicher Tagesplaner** für iPhone und iPad mit eigenständiger **Klangbild-Identität**. Solo-Nutzung. App-Store-Produkt. Die Vision ist nicht "wird zu etwas anderem" — die Vision ist "wird ein richtig gutes, reifes Werkzeug dafür, was es ist".

Die Entwicklung umfasst zwei Stränge:

1. **Die App selbst** — wird in Phase 0 featurekomplettiert (Musik, Photo-Attachments, EventKit, Sprachen, Klangwelten) und dann sauber gepflegt.
2. **Ein MCP-Server als persönliches Werkzeug** — ermöglicht dem einzelnen Nutzer (zuerst: Chris) den Zugriff auf seine MyCadenza-Daten über Claude. Keine App-Erweiterung, sondern Infrastruktur.

**Durchgängige Prinzipien:**

- **Klangbild bleibt zentral.** Die Cadenza-Metapher mit Klangwelten, Tag-Beginn/Ende-Sounds und perspektivisch der algorithmischen Tagesmelodie ist Identitätsmerkmal, nicht Beiwerk.
- **Datensouveränität.** iCloud als Standard-Sync (Solo-Nutzung, Apple-Infrastruktur). MCP-Server in Deutschland (Hetzner Nürnberg).
- **§203 StGB als Architekturfundament beim MCP-Server.** Da der Server potenziell Kanzleifristen-Aufgaben sehen könnte, gelten die gleichen Anforderungen wie beim Kanzlei-Strang — auch wenn es "nur" um einen Einzelnutzer geht.
- **Privacy by Design.** Keine Analytics, keine Telemetrie, keine Drittdienste. Solo-Daten bleiben auf Apple-Geräten und in iCloud.
- **Plan vs. Realität bleiben in der UX klar getrennt.** Die App soll abbilden, was *getan* wurde, nicht nur was *geplant* war. (Siehe §8 v1.7.1-Block in `MyCadenza_TodoList_v1_7.md`.)

---

## 2. Ausgangslage (Stand April 2026)

| Bereich | Stand |
|---|---|
| **App-Typ** | SwiftUI + SwiftData iOS/iPadOS App, iOS 17+ |
| **Sync** | CloudKit Private Database (`iCloud.com.hoferer.cmt`) |
| **Identität** | Implizit via Apple-ID des Geräts |
| **Release-Stand** | v1.6.1 Build 10602 **im App Store freigegeben** |
| **In Entwicklung** | v1.7.0 Build 10716 (gepusht, SubTask-Operations konsolidiert) |
| **In TestFlight** | v1.7.0 Build 10715 (TaskScheduler `@MainActor`-Refactor, BG-Crash-Fix) |
| **Website** | `mycadenza.app` live (Hugo auf Hetzner Webhosting S) |
| **Code-Repository** | GitHub privat |
| **Code-Qualität** | 15 von 108 Review-Befunden erledigt (Stand 25.04.2026) |

---

## 3. Abgrenzung zum Kanzlei-Strang

MyCadenza und das **Kanzlei-Kommunikationswerkzeug** (eigenes Projekt von Chris) sind als **eigenständige Produkte** gedacht und werden **nicht in einer Architektur zusammengeführt**.

Gemeinsamkeiten, die geteilt werden können:

- **UI-Sprache und Designdisziplin** (warmes Farbbild, ruhige Typographie, Fraunces/DM Sans)
- **Allgemeine Hetzner-Infrastruktur** (Account, AVV, Monitoring — aber getrennte Server-Instanzen)
- **Erfahrung aus dem SwiftData-Modell** — Kategorien-Denken, Status-Logik, Aufgaben-Metaphern

Klare Trennungen:

- **Kein gemeinsames Datenmodell.** Das MyCadenza-Modell ist ungeeignet für Messaging. Der Kanzlei-Strang baut sein eigenes Modell von Grund auf.
- **Keine App-Erweiterung mit Kanzleifunktionen.** MyCadenza bleibt Solo-App.
- **Kein gemeinsamer Auth-Flow.** MyCadenza braucht keine Auth (Apple-ID des Geräts genügt). Kanzlei-Werkzeug bekommt eigene Auth (SSO/Passkeys, siehe Kanzlei-Dokument).

---

## 4. Strategische Eckpfeiler

### 4.1 CloudKit bleibt alleinige Datenquelle der App

Für Solo-Nutzung ist CloudKit Private Database ausreichend, kostenfrei, zuverlässig und datenschutzfreundlich. Kein Server-Backend geplant für die App selbst.

### 4.2 MCP-Server als *zusätzlicher* Zugriffsweg

Der MCP-Server liest Daten aus derselben CloudKit-Datenbank (via CloudKit Web API REST) — nicht aus einem separaten Store. Damit bleibt die App die alleinige Schreib-Autorität für die Daten-Struktur, der MCP-Server ist Gast.

### 4.3 `async/await` bei Netzwerk-Code

Sobald HTTP-Calls dazukommen (MCP-Server-Client in der App, falls je gewünscht, oder Code im MCP-Server selbst), ist `async/await` das natürliche Muster. Für den App-Kern (ohne Netzwerk) bleibt synchroner Code bzw. MainActor ausreichend.

### 4.4 §203 StGB auch im persönlichen MCP-Server

Da Chris MyCadenza beruflich für Wiedervorlagen einsetzt, können Aufgaben Mandantenbezug haben. Der MCP-Server muss die gleichen Sicherheitsanforderungen erfüllen wie ein Team-System: Hosting in Deutschland, Verschlüsselung at rest, Audit-Log, verschlüsselte Backups, keine unbeteiligten Dritten mit Zugriff.

### 4.5 Etappen-Buildnummern für Rollback-Fähigkeit

Jede größere Entwicklungsetappe bekommt eine eigene Buildnummer im Xcode und einen Git-Tag. Falls ein Entwicklungspfad verworfen werden muss: `git reset --hard` auf den vorigen Tag, Buildnummer zurücksetzen.

---

## 5. Architektonische Kernfragen

Deutlich schlanker als im ursprünglichen Entwurf — die meisten strategischen Fragen erübrigen sich, weil kein Multi-User-System gebaut wird.

### 5.1 ModelContainer-Zugänglichkeit (für BG-Tasks) — ✅ erledigt 10705

**Lösung:** Static Shared Instance in `PersistenceController.shared`. Foreground und Background teilen sich Schema + CloudKit-Konfiguration.

### 5.2 CloudKit-Batch-Strategie beim Replace-Import — ✅ erledigt 10706

**Lösung:** Drei Zwischen-Saves (nach Delete, nach Kategorien, nach Aufgaben). CloudKit-Operationen werden in kleineren Batches gepusht statt einem großen Block.

### 5.3 Status-Semantik (`isManualStatus`) — ✅ erledigt 10711 + 10716

**Lösung:** Model-API `markDone(manually:)`, `markSkipped(manually:)`, `markOpen(manually:)` auf `MainTask`. Setter-Side-Effect aus `status.set` entfernt. `applyExpiry()` ruft `markSkipped(manually: true)`. Name `isManualStatus` beibehalten (Umbenennung zu `isStatusLocked` wäre CloudKit-Schema-Migration gewesen — Option C der Refactor-Diskussion). 8 von 9 Raw-Muster-Call-Sites migriert in 10711; der letzte Ausreißer (Cleanup-Unlock in EditorView) ist mit C-S37 in 10716 aufgeräumt worden — die Logik wanderte in die `MainTask`-Extension `cleanupCompletedSubTasks(context:)`, die jetzt über alle drei Aufrufstellen konsistent `isManualStatus = false` vor `syncStatus()` setzt.

### 5.4 Sound-Kaskaden-UX — ⬜ offen

**Heute:** Neue Sounds brechen laufende ab.
**Empfehlung:** Produkt-Entscheidung nach Klangwelten-Review. Dann entweder Player-Pool oder Status quo bestätigen. Geplant in Build 10722.

### 5.5 Thread-Modell für MCP-Server-Client (falls App je einer wird)

Die App selbst hat heute keinen Netzwerk-Client. Der MCP-Server läuft extern und wird von Claude angesprochen, nicht von der App. **Solange das so bleibt, gibt es keine async/await-Zwangsumstellung.**

Falls die App später einen MCP-Client bekommen soll (z.B. um Server-Status anzuzeigen), wird an dieser Stelle `async/await` eingeführt. Heute nicht relevant.

### 5.6 Plan vs. Realität — 🆕 neue Leitfrage seit 24.04.2026

Aus der Test-Verifikation zu Build 10711 hat sich gezeigt: Die App denkt in mehreren Stellen zu stark in `startTime` (Plan) statt in `lastExecutedDate` (Realität). Drei konkrete UX-Findungen:

1. **Historie ist kein echtes Protokoll** — verschwindet beim Löschen der Wiederholungsgruppe
2. **Default-Startzeit forciert "morgen"** — wenn die Default-Uhrzeit in der Vergangenheit liegt, wird die Aufgabe auf den nächsten Tag verschoben
3. **Abgeschlossen zeigt nur "heute geplant, heute erledigt"** — heute vorgearbeitete Aufgaben für morgen sind unsichtbar

Diese drei Themen hängen zusammen und werden als v1.7.1-Block gemeinsam geplant. Details in `MyCadenza_TodoList_v1_7.md` Block B.

---

## 6. Phasenplan

Nur zwei Phasen. Phase 0 ist auf dem Weg zur Feature-Komplettierung, Phase 1 ist der MCP-Server. Keine weiteren Phasen geplant.

### Phase 0 — Feature-Vervollständigung (laufend, Zeitrahmen offen)

**Zustand:** Solo-App, stabil, wird um verbleibende Features ergänzt.

**Offene Arbeitspakete (aus vorhandenen TODO-Listen):**

- **Code-Qualität** — Umsetzung der verbleibenden ~93 Befunde aus Review v1.7.0
- **Plan-vs-Realität-Block** (v1.7.1) — Historie als Protokoll, Default-Startzeit, Vorgearbeitet-Sektion
- **Klangwelten** — System-Klangwelt vollständig mit WAVs abdecken, 4 Klangwelten nachbessern
- **xcstrings-Bereinigung** — 1–2 verbleibende Artefakte
- **In-App Sprachauswahl** — statt iOS-Settings-Redirect
- **Sprachen-Stufe A** (romanische Familie: FR, ES, IT, PT)
- **EventKit-Integration** — Reminders + Calendar (Mandanten-Wiedervorlagen, Fristen)
- **Contacts-Integration** — Mandanten-Verknüpfung
- **Photo-Attachments** — 3 Ebenen, Verfallsdatum-System ab Erledigung/Verfall
- **CarPlay** — Musik-Steuerung aus dem Auto
- **Strict Concurrency Complete** — eigene Etappe nach v1.7.0-Release
- **Feinere Zeitstufen** (5/15 Min) und Debug-Helfer für sofortigen Verfall — Konzept-Etappe
- **Sprachen-Stufen B–E** — Benelux, Skandinavien, Ost-/Südost-Europa, Rest
- **Tagesmelodie-Vision** — generierte Kurzmelodie (langfristig)

**Entwicklungsrhythmus:**
- Jede Etappe eigene Buildnummer (aktuell bei 10716, geplant bis 10724+)
- Git-Tag pro Etappe
- Clean Build + Verifikation vor nächster Etappe
- Bei Crash-relevanten Etappen: Drei-Stufen-Test (Simulator → echtes iPhone → TestFlight)
- CHANGELOG.md nachziehen

**Abschluss-Trigger Phase 0:** Die App ist feature-komplett für den Solo-Einsatz. Kein offenes Kern-Feature-TODO. Zielversion z.B. v2.0.

### Phase 1 — MCP-Server (zeitlich nachgelagert, aber parallel zu Phase 0 möglich)

**Zustand:** MyCadenza bleibt Solo-App. Zusätzlich: Chris kann über Claude auf seine Daten zugreifen.

**Warum parallel möglich:** Der MCP-Server ist **außerhalb der App** — eigenes Node.js-Projekt, eigenes Git-Repo, eigener Server. Er tangiert den App-Code nur, wenn wir in der App einen Client bauen wollen (was nicht geplant ist). Damit kann Phase 1 beginnen, wann immer die Lust dazu größer ist als die Lust auf das nächste App-Feature.

**Arbeitspakete:**

| # | Paket | Umfang |
|---|---|---|
| 1.1 | **Hetzner-Cloud-Setup** | CX11 Nürnberg, Node.js-Stack, AVV, TLS (Let's Encrypt), Monitoring |
| 1.2 | **CloudKit Web API Zugriff** | REST-Auth-Flow (Apple-Token-Signatur), Read auf eigene Private DB |
| 1.3 | **MCP-Server v1** | Tools: Aufgaben lesen (heute, fällig, überfällig, Ausblick), Aufgabe erstellen, Status ändern, Kategorien/Templates lesen, Historie abfragen |
| 1.4 | **Festes API-Token** | Nur Chris, kein OAuth |
| 1.5 | **Rate Limiting + Fehlerbehandlung** | – |
| 1.6 | **Test in Claude Desktop/Cowork** | End-to-End: "Was steht heute an?", "Erstelle Aufgabe XY" |

**Use Cases — Alltag:**
- "Was steht heute an?" → Claude liest Tagesaufgaben
- "Erstelle eine Aufgabe: Mandantenakte XY prüfen, morgen 09:00, Kategorie Arbeit"
- "Wie war meine Erledigungsquote letzte Woche?"
- "Verschiebe alle offenen Aufgaben von heute auf morgen"
- Cowork: Tages-/Wochenberichte aus MyCadenza-Daten

**Abschluss-Trigger:** Der MCP-Server ist stabil, wird im Alltag genutzt, Chris vertraut der Verbindung.

**Bewusste Nicht-Ziele:**
- Kein Multi-User-Support
- Keine Freigabe an andere Nutzer
- Keine Phase B (OAuth für Multi-User) — die damalige Planung ist mit der heutigen Klarstellung obsolet

---

## 7. Impact auf das aktuelle Review

Mit der Klarstellung, dass MyCadenza keine Multi-User-/Team-Zukunft hat, änderte sich die Strategie-Bewertung **zu Gunsten einfacher Lösbarkeit**:

| Befund | Alter Tag | Neuer Tag | Aktueller Status |
|---|---|---|---|
| A-S1 (`status.set` Side-Effect) | 🔴 strategisch | 🟢 unabhängig | ✅ erledigt in 10711 |
| A-A1 (`isManualStatus` Name) | 🔴 strategisch | 🟢 unabhängig | ✅ erledigt in 10711 (Name behalten, Semantik über Model-API gelöst) |
| B-S1 (Race Conditions) | 🔴 strategisch | 🟢 unabhängig | ✅ erledigt in 10715 (TaskScheduler `@MainActor`, Variante B) |
| C-S37 (cleanup/reset Duplikate) | 🟡 sollte | 🟢 unabhängig | ✅ erledigt in 10716 (Extension auf `MainTask`, drei Sub-Bugfixes) |

**Konsequenz:** Alle Befunde können im Rahmen von Phase 0 angegangen werden. **15 von 108** sind bereits erledigt, der Rest ist in den Etappen 10717–10726+ geplant. **B-M2 (Import-Sperre bei aktivem CloudKit-Sync)** ist der **letzte v1.7.0-Release-Blocker** und ist für Build 10721 vorgesehen.

---

## 8. Offene Fragen

1. **Tagesmelodie-Vision — Zeitpunkt?** Bleibt bewusst offen. Perspektivisch in Phase 0, aber erst nach den anderen Features.
2. **App-Version bei Phase-0-Abschluss.** v1.x weiterführen oder zu v2.0 springen als Zäsur?
3. **CHANGELOG-Pflege.** Pro Etappe oder pro Minor-Release (1.7.0)?
4. **Sprachen-Stufen B–E.** Reihenfolge und Zeitpunkt flexibel — nach Energie und Nutzerfeedback.
5. **Plan-vs-Realität-Block Priorität.** Nach v1.7.0-Release als Hauptblock von v1.7.1, oder in einzelnen Etappen davor verteilt? Jedes der drei Teilthemen ist eine eigene UX-Entscheidung, die Design-Vorarbeit braucht. F4, F7, F9 hängen thematisch dazu.
6. **Strict Concurrency Complete — Zeitpunkt?** Eigene Etappe nach v1.7.0-Release. Voraussetzung: alle Singletons (`MusicManager`, `PersistenceController`, `DataMigrator`, `DemoDataProvider`) systematisch annotiert.

---

## 9. Änderungshistorie

| Datum | Änderung |
|---|---|
| 2026-04-18 · vormittags | Erstellt mit vollständiger Multi-User/Team-Roadmap |
| 2026-04-18 · nachmittags | Stark entschlankt: Multi-User/Teams entfernt (Chris-Klarstellung). Fokus auf Solo-App + MCP-Server. Etappen-Buildnummern als Arbeitsmethode eingeführt. |
| 2026-04-24 | Build 10711 reflektiert: §5.1, §5.2, §5.3 als erledigt markiert. §5.6 "Plan vs. Realität" als neue Leitfrage ergänzt (drei UX-Findungen aus der 10711-Test-Session). §7 Impact-Tabelle: aktuelle Status-Spalte. §1 Vision: Plan-vs-Realität als durchgängiges Prinzip. Verweise auf TodoList-Block B ergänzt. |
| 2026-04-25 | Builds 10712a/b/c, 10715, 10716 reflektiert. CloudKit-Schema-Check (Production deckungsgleich mit Development) durchgeführt. TestFlight-Crash-Diagnose mit klarer Ursache: B-M1-Fix (10705) hatte das B-S1-Race-Condition-Problem freigelegt. §5.3 erweitert um Cleanup-Unlock-Bereinigung in 10716. §5.4 Build-Nummer aktualisiert (10722 statt 10718). Phasenplan um Strict Concurrency Complete und Feinere Zeitstufen erweitert. §7 Impact-Tabelle: B-S1 als erledigt, C-S37 hinzugefügt. §8 Offene Fragen: Punkt 5 präzisiert, Punkt 6 (Strict Concurrency) neu. **15 von 108 Review-Befunden erledigt.** |
