# MCK · Klangwelt-Methodik

> Werkzeuge, Schablonen und Prozesse zur Ausgestaltung der Klangwelten von MyCadenza.
> Stand: 06.05.2026 · Status: Erstfassung

---

## Zweck und Bezugsrahmen

Diese Methodik beantwortet die Frage *Wie* — wie Designentscheidungen aus Manifest und Charta in konkrete Sounds übersetzt werden, die im Code spielen. Sie steht zwischen den drei anderen aktiven Säulen:

- [`MCK_Manifest.md`](MCK_Manifest.md) liefert die Designprinzipien (Causality, Harmony, Utility, Sound-Weight) und die Negativliste — das *Warum*.
- [`MCK_Chartas.md`](MCK_Chartas.md) liefert pro Welt die klangliche Identität (Sound-Typ, Klangmaterial, Tonalität, Charakter, Kernmotiv) — das *Was-pro-Welt*.
- [`MCK_Inventur.md`](MCK_Inventur.md) Sektion 5 liefert die Sound-Verdienst-Tabelle — das *Was-wann*.

Die Methodik verdichtet die Entscheidungen aus diesen Säulen zu wiederverwendbaren Heuristiken, dokumentiert die ElevenLabs-Werkzeugkette mit der 8-Slot-Schablone, legt das Audio-Session-Verhalten im Code fest und beschreibt die Iterations-Praxis bei Generierung, Bewertung und Pflege der WAV-Sätze.

Was sie nicht abdeckt: konkrete Prompts pro Aktion × Welt — dafür gibt es [`MCK_Prompts.md`](MCK_Prompts.md). Und konkrete WAV-Bewertungen — die liegen in [`MCK_Bewertung.xlsx`](MCK_Bewertung.xlsx). Methodik beschreibt, *wie* man sie schreibt und bewertet, nicht *was* in ihnen steht.

### Ableitungslogik

Die Säulen bauen aufeinander auf:

1. Manifest → Charta — eine Welt entsteht aus den Designprinzipien, nicht aus Geschmack.
2. Charta + Sound-Verdienst → Methodik — die Charta liefert die Klangsprache, die Sound-Verdienst-Tabelle liefert die Pflichtmenge an Sounds, die Methodik liefert das Werkzeug, beides zusammen umzusetzen.
3. Methodik → Prompts — jeder Prompt ist eine Anwendung der 8-Slot-Schablone auf eine konkrete Aktion in einer konkreten Welt.
4. Prompts → WAVs → Bewertung — Generierung mit ElevenLabs, Auswahl, Test, Eintrag in die Bewertungsmatrix.

Wer eine Methodik-Entscheidung trifft, prüft sie gegen Manifest und Charta. Wer einen Prompt schreibt, prüft ihn gegen die Methodik. Wer einen WAV-Satz bewertet, bezieht sich auf den Prompt und die Charta der Welt.

---

## Sound-Verdienst-Entscheidung

Das Manifest-Prinzip *Utility* sagt: Klang nur an signifikanten Momenten. Diese Methodik kodifiziert, *welche* Klassen von Klang an welchen Momenten zum Einsatz kommen — und welche Trigger-Klassen bewusst stumm bleiben.

Die kanonische Anwendungs-Tabelle für die Code-Stellen in v1.7.x steht in [`MCK_Inventur.md`](MCK_Inventur.md) Sektion 5. Dieses Kapitel verdichtet die Logik dahinter, sodass sie auch auf neu hinzukommende Aktionen angewendet werden kann.

### Vier Klang-Klassen

Jeder Trigger fällt in eine von vier Klassen.

| Klasse | Wann | Charakter | Beispiele |
|---|---|---|---|
| **Bestätigung Level 1** (subtil) | Eingabe einer Inhalt-Veränderung wurde registriert | Kurz, leicht, dezent — keine große Auflösungsgeste | Edit-Sheet schließt mit Änderung; nach destruktiver Einzel-Aktion (Cleanup, Reset, Trash) |
| **Bestätigung Level 2** (Erfolgsmeldung) | App-globale oder externe Aktion durchgeführt | Voller, mit Resolutions-Geste, eindeutig positiv | Apple Music verbunden; Alle Daten zurückgesetzt; Export/Import-Erfolg |
| **Warnung vor Aktion** | ConfirmationDialog vor destruktiver Aktion erscheint | Eindringlicher als Bestätigung, deutet Spannung an, ohne sie aufzulösen | Trash-Tap; Cleanup-/Reset-/Duplikate-Confirm |
| **Stille** | Keine der oberen Bedingungen trifft zu | — | Reorder, Sheet-Öffnen, Validierung, Tab-Wechsel, App-Start, Section-Header-Toggle |

### Heuristik: Level 1 vs. Level 2

Die zentrale Unterscheidung ist *Tragweite*. Eine veränderte Aufgabe ist Level 1 — der Nutzer arbeitet im normalen Editier-Flow. Ein zurückgesetzter Datenbestand ist Level 2 — der Nutzer hat eine app-globale Aktion ausgelöst, die viele Daten verändert oder ein externes System anspricht.

Faustregel:

- **Level 1** — einzelne Aufgabe, einzelner Inhalt, lokale Veränderung
- **Level 2** — app-globale Datenoperation, externes System, mehrstufiger Vorgang

Die Heuristik wird nicht durch Doppelmarkierung verschoben: Wenn eine destruktive app-globale Aktion vorher eine Warnung auslöst und danach eine Bestätigung braucht, ist die Bestätigung Level 2 — die vorhergehende Warnung dehnt nicht das Bestätigungs-Level. Umgekehrt bleibt eine destruktive Einzel-Aktion (Trash einer Aufgabe) Level 1, auch wenn ihr eine Warnung vorausging.

### Doppelmarkierung bei destruktiven Aktionen

Destruktive Aktionen haben zwei Sound-Momente: das Erscheinen des ConfirmationDialogs (Klasse *Warnung vor Aktion*) und das Durchführen nach Bestätigung (Klasse *Bestätigung Level 1* oder *Level 2*, je nach Tragweite).

Dieses Pattern macht den Entscheidungsmoment hörbar und quittiert seine Ausführung. Es greift nur dort, wo der Dialog tatsächlich eine destruktive oder weitreichende Aktion absichert. Reine Hinweis-Alerts ohne Eingriff (etwa System-Update-Hinweise) fallen *nicht* unter Doppelmarkierung — sie bekommen einen einzelnen Hinweis-Klang nach eigener Logik (siehe `.updateVerfuegbar` in der Inventur).

### Bewusst stumm — Stille als Designentscheidung

Die folgenden Trigger-Klassen bleiben stumm, dauerhaft und über alle Welten hinweg. Stille ist hier kein Versäumnis, sondern aktive Entscheidung gegen *Klang als Verzierung* (Manifest, Causality-Prinzip):

- **Reorder / Drag-and-Drop** — UI-Bewegung ohne semantische Vollendung
- **Sheet-Öffnungen** — Navigationsschritt, keine Aktion
- **Validierungsfehler** — die Disabled-Architektur fängt sie im UI ab; siehe [`MCK_Inventur.md`](MCK_Inventur.md) Sektion 2 Anmerkung
- **App-Start** — Splash, Onboarding-Schritte; der Tag-Beginn-Sound triggert die Tagesablauf-Logik, nicht das App-Öffnen
- **WhatsNewView** — Update-Erlebnis ist visuell
- **DEBUG-Buttons** — Entwickler-Werkzeug, kein Nutzer-Workflow
- **Tab-Wechsel** und **Section-Header Expand/Collapse** — UI-Manipulation ohne Status-Veränderung
- **Aufgaben-Kategorie-Wechsel** — die Klangwelt-Wahl findet im Kategorie-Picker statt, nicht beim Aufgaben-Edit
- **Teilaufgaben hinzufügen** — zu kleinteilig, würde bei aktivem Aufgaben-Detail-Aufbau zur Belastung
- **TaskScheduler-Side-Effects** — Hintergrund-Vorgänge ohne Nutzer-Auslöser; Sound aus dem Nichts wäre verwirrend

Wenn ein neuer Trigger die Frage aufwirft *„braucht das einen Sound?"*, ist die Default-Antwort *nein*. Sound entsteht aus Notwendigkeit, nicht aus Vollständigkeit. Die Pflicht der Begründung liegt bei *Klang*, nicht bei *Stille*.

---

## Sound-Längen — Vorab-Konvention und Verifikation durch Tests

Aus dem Manifest-Prinzip *Sound-Weight* folgt: Klanggewicht und Bedeutung der Aktion müssen übereinstimmen. Diese Methodik verfeinert das Prinzip in eine Längen-Konvention mit konkreten Sekunden-Bändern — als Vorab-Schätzung, die in einer Experimentier-Phase mit ElevenLabs verifiziert oder korrigiert wird.

### Klingende Dauer vs. technische Dauer

ElevenLabs liefert in der Praxis WAV-Dateien, in denen ein Teil der Datei keinen hörbaren Klang trägt — Stille am Ende, Reverb-Tails, die unterhalb der Hörbarkeit ausklingen, gelegentlich auch Vorlauf-Stille am Anfang. Daher unterscheidet diese Methodik zwei Längen-Begriffe:

- **Klingende Dauer** — der Zeitabschnitt, in dem für den Hörer wahrnehmbarer Klang da ist. Das ist das Maß, das die Wahrnehmung trägt und auf das sich die Bänder unten beziehen.
- **Technische Dauer** — die Länge der WAV-Datei. Sie umfasst die klingende Dauer plus mögliches Stille-Padding und ist erst nach Trimming relevant.

Diese Trennung wird im Vokabular der Methodik konsequent durchgehalten. Wenn an einer Stelle „Dauer" ohne Adjektiv steht, ist die klingende Dauer gemeint.

### Vorab-Bänder

Stand 06.05.2026, durch Tests zu verifizieren.

| Klang-Klasse | Klingende Dauer | Begründung |
|---|---|---|
| Mikro-Bestätigungen (Level 1 subtil) | 0.3–0.7 s | Häufiger Trigger (Edit-Sheet schließt, Cleanup nach Warnung) — darf nicht zur Belastung werden |
| Workflow-Sounds (`teilaufgabeErledigt`, `teilaufgabeUebersprungen`) | 0.5–1.0 s | Mikro-Aktion mit erwartbarer Häufigkeit; bestehende WAVs liegen erfahrungsgemäß in diesem Band |
| Mittlere Aktionen (Warnung vor destruktiver Aktion, `.aktionRegistriert` nach Warnung) | 0.8–1.5 s | Klein genug für Häufigkeit, groß genug für Eindringlichkeit |
| Größere Vollendung (`hauptaufgabeKomplett`, `.aktionAbgeschlossen`, `.updateVerfuegbar`) | 1.5–2.5 s | Trägt Resolutions-Geste, einmaliger Moment im Tagesablauf |
| Tagesgesten (`tagBegonnen`, `tagBeendet`, `tagKomplett`) | 2.0–3.0 s | Seltenste und gewichtigste Klänge des Tages |
| Sync-Status (`.syncCompleted`, `.syncFailed`) | 0.5–1.2 s | Quittierung eines App-internen Vorgangs; nicht workflow-tragend, daher kompakt |

Diese Tabelle ist als Konvention gemeint, an der sich die Generierung orientiert. Die Experimentier-Phase (Sektion 7) prüft, ob die Bänder in der Praxis tragen, und korrigiert sie, falls nötig.

### Trimming-Pflicht im Workflow

Bevor eine generierte WAV ins App-Bundle wandert, wird sie auf ihre klingende Dauer geprüft und gegebenenfalls beschnitten. Lange Stille-Tails am Ende werden entfernt, lange Stille-Phasen am Anfang ebenso. Ein knappes Polster bleibt akzeptabel:

- **Anfang:** ~30–80 ms Stille-Vorlauf für sauberen Abspielstart ohne Klick-Artefakte
- **Ende:** ~50–150 ms Ausklang, falls der Sound einen weichen Abschluss braucht; bei Sounds mit definiertem Cutoff (Charakter „clean cutoff at the end") gar kein Tail

Im Prompt selbst lässt sich das Padding über den Slot *Negative Constraints* (siehe Sektion 6) reduzieren — etwa mit Formulierungen wie „no silence at start", „no fade tail beyond N seconds", „clean cutoff at end". Prompts garantieren das Ergebnis aber nicht; die finale Verantwortung liegt beim Trimming nach der Generierung.

### Lautheit als sekundärer Längen-Faktor

Eine Beobachtung aus der bisherigen Praxis (Build 10716-Test, Inventur Sektion 2 Anmerkung): System-Töne fallen bei kurzen Aktionen kaum hörbar aus. Daraus folgt: die untere Grenze der Bänder ist nicht nur eine Frage der Sekunden, sondern auch der Lautheit und Transienten-Schärfe. Ein 0.4-Sekunden-Sound mit weichem Onset und niedriger Lautheit wird unhörbar; derselbe Slot mit scharfem Transient und voller Lautheit trägt die Bedeutung.

Diese Wechselwirkung ist mit der Robustheits-Anforderung der Charta (Sektion „System") verknüpft und wird im Prompt durch die Slots *Attack* und *Charakter* adressiert (Sektion 6). Die Längen-Konvention allein reicht nicht — sie ist ein Teil der Sound-Spezifikation, kein Garant.

---

## Aktions-Klassifikation und Klangwelt-Zuordnung

Diese Sektion verbindet zwei Designschichten: *welche* Aktionen es gibt, und *aus welcher Welt* sie ihren Klangcharakter ziehen.

### Drei Klang-Klassen

Jede SoundAction in MyCadenza fällt in eine von drei Klassen. Die Klassifikation ergibt sich aus der Frage, *wer* die Aktion auslöst und *was* sie kommuniziert — nicht aus der Code-Gruppe, in der sie entstanden ist.

| Klasse | Charakter | Was sie kommuniziert |
|---|---|---|
| **A — Ritueller Workflow** | User-getriebene Aufgaben-Vollendung und Tagesrahmung | Bedeutsamer Moment im Tagesablauf — die Welt-Identität (Kategorie- oder Standard-Klangwelt) zeigt sich hier am klarsten |
| **B — Ereignis-Aufmerksamkeit** | System-getriebene Hinweise an den User | Information ohne Anforderung an unmittelbare Aktion |
| **C — Generalisierte Bestätigung** | User-Aktion, kontext-frei generalisiert | Quittierung einer Eingabe — ein Sound für viele Trigger-Stellen |

### Aktions-Liste (Stand 06.05.2026)

Sechzehn SoundActions verteilt auf die drei Klassen. Diese Liste ist die *kanonische Quelle* für `MCK_Prompts.md` und für die WAV-Generierung — jede Aktion bekommt einen Prompt pro aktiver Welt (16 × 5 = 80 WAV-Sätze).

**Klasse A — Ritueller Workflow (7):**

| Aktion | Welt-Quelle | Anlass |
|---|---|---|
| `tagBegonnen` | Standard-Klangwelt | Erster App-Start des Tages |
| `teilaufgabeErledigt` | Kategorie-Welt | Subtask abgehakt |
| `teilaufgabeUebersprungen` | Kategorie-Welt | Subtask oder Hauptaufgabe übersprungen |
| `hauptaufgabeKomplett` | Kategorie-Welt | Hauptaufgabe abgeschlossen (alle Subtasks done oder direkt markiert) |
| `tagKomplett` | Standard-Klangwelt | Tagesfortschritt 100 % erreicht |
| `tagBeendet` | Standard-Klangwelt | Tagesabschluss-Geste (Tagesbericht-Tap) |
| `ueberfaelligErledigt` | Kategorie-Welt | Aufgabe nach Überfälligkeit doch noch erledigt — der „Stoßseufzer", eigenständige Klangidentität |

**Klasse B — Ereignis-Aufmerksamkeit (6):**

| Aktion | Welt-Quelle | Anlass |
|---|---|---|
| `aufgabeFaellig` | Kategorie-Welt | Aufgabe wird fällig |
| `aufgabeUeberfaellig` | Kategorie-Welt | Aufgabe wird überfällig |
| `aufgabeVerfallen` | Kategorie-Welt | Aufgabe verfällt (nicht mehr ausführbar) |
| `.updateVerfuegbar` | Standard-Klangwelt | App-Update im Store entdeckt |
| `.syncCompleted` | Standard-Klangwelt | CloudKit Initial-Sync abgeschlossen |
| `.syncFailed` | Standard-Klangwelt | CloudKit-Sync-Fehler |

**Klasse C — Generalisierte Bestätigung (3):**

| Aktion | Welt-Quelle | Anlass |
|---|---|---|
| `.aktionRegistriert` | Kontext-sensitiv | Eingabe registriert: Edit-Sheet schließt mit Änderung; nach destruktiver Einzel-Aktion |
| `.aktionAbgeschlossen` | Kontext-sensitiv | App-globale oder externe Aktion durchgeführt |
| `.warnungVorAktion` | Kontext-sensitiv | ConfirmationDialog vor destruktiver Aktion erscheint |

### Welt-Quellen

In der App existieren zwei Welt-Quellen, die sich nicht überschneiden:

**Kategorie-Welt** — Jede Kategorie hält eine eigene Klangwelt in `category.soundWorld`. Aufgaben-bezogene Aktionen ziehen die Welt der Kategorie der jeweiligen Aufgabe. Dadurch tragen Aufgaben verschiedener Kategorien unterschiedliche Welt-Charaktere, auch wenn sie dieselbe Aktion auslösen — der Tag bekommt klangliche Vielfalt durch die Kategorisierung.

**Standard-Klangwelt** — Eine globale Settings-Variable, in der App über die SettingsView konfigurierbar. Greift für alle Aktionen ohne Aufgaben-Bezug: Tag-Rahmungen, app-system-interne Hinweise, sowie Klasse-C-Bestätigungen ohne Aufgabenkontext.

> **Code-Konsequenz aus dieser Methodik:** Die heutige Variable `dayBookendSoundWorld` deckt nur die Tag-Sounds ab und muss umbenannt und erweitert werden. Code-Bezeichnung und UI-Label folgen der Bezeichnungs-Konvention dieser Methodik (siehe oben). Diese Umbenennung ist Teil der nächsten Code-Etappe zur Sound-Architektur.

### Klasse C — Welt-Quelle ist kontext-sensitiv

Klasse-C-Aktionen entscheiden pro Trigger-Stelle, nicht pro Aktion, welche Welt-Quelle greift. Faustregel:

- Trigger hat direkten Aufgaben-Bezug → Kategorie-Welt der Aufgabe
- Trigger ist app-global oder bezieht sich auf System-Funktionen → Standard-Klangwelt

Konkret pro Trigger-Stelle aus [`MCK_Inventur.md`](MCK_Inventur.md) Sektion 5:

| Trigger-Stelle | Welt-Quelle |
|---|---|
| Edit-Sheet `EditTaskView` schließt mit Änderung | Kategorie-Welt der editierten Aufgabe |
| Edit-Sheet `EditTemplateView` schließt mit Änderung | Standard-Klangwelt (Templates sind nicht kategorisiert) |
| Edit-Sheet `EditCategoryView` schließt mit Änderung | Welt der editierten Kategorie |
| Trash einer Aufgabe (Doppelmarkierung) | Kategorie-Welt der Aufgabe |
| Cleanup / Reset in Heute-Liste oder Teilaufgaben-Sheet (Doppelmarkierung) | Kategorie-Welt der betroffenen Hauptaufgabe |
| Apple-Music verbunden | Standard-Klangwelt |
| Duplikate bereinigen (Doppelmarkierung) | Standard-Klangwelt |
| Alle Daten zurücksetzen (Doppelmarkierung) | Standard-Klangwelt |
| Daten-Export- / Import-Erfolg | Standard-Klangwelt |

### Mehrere Aufgaben gleichzeitig

Bei Cleanup-Aktionen aus dem Heute-Pfad oder Teilaufgaben-Sheet, die mehrere Aufgaben oder Subtasks aus möglicherweise verschiedenen Kategorien betreffen: Welt-Quelle ist die Kategorie-Welt der **Hauptaufgabe**, in deren Kontext die Aktion ausgelöst wurde — nicht ein Mischklang aus mehreren Welten.

Begründung: Der Nutzer hat den Cleanup im Sheet *einer* Hauptaufgabe ausgelöst — sein mentales Modell ist diese eine Aufgabe. Die Welt der Subtasks ist (per Datenmodell) sowieso die der Hauptaufgabe.

Bei Cleanup im SettingsTab (Duplikate-Bereinigen / Alle-Daten-Zurücksetzen) gibt es keinen Aufgabenkontext → Standard-Klangwelt.

### Konsequenz für Prompts

Aus der Klassifikation und der Welt-Zuordnung folgt: Ein Prompt in [`MCK_Prompts.md`](MCK_Prompts.md) wird *pro Aktion × Welt* geschrieben — 16 × 5 = 80 Prompts. Dabei ist die Welt-Quelle des Trigger-Codes nicht Teil des Prompts (das ist Sache der App-Logik), wohl aber die Welt-Identität, in der der Sound erklingen soll. Der Prompt für `.aktionRegistriert` in der Welt Salon ist ein einziger Prompt, unabhängig davon, ob er später nach EditTaskView-Schließen oder nach Trash-Bestätigung gespielt wird.

Das ist die Pointe der Klasse-C-Generalisierung: *kontext-sensitive Welt-Quelle, kontext-freier Sound*.

---

## Audio-Session-Konfiguration

Der Code-seitige Rahmen, in dem die Klangwelt spielt. Diese Sektion hält die in der Sound-Verdienst-Diskussion (Inventur-Sektion-5) getroffenen Beschlüsse fest und ergänzt die technischen Details, die für die Robustheit der Klangwelt nötig sind.

### Audio-Session-Kategorie und Optionen

`AVAudioSession` wird im SoundManager-`init()` einmalig konfiguriert:

- **Kategorie:** `.ambient`
- **Optionen:** `.duckOthers`

Damit wird Hintergrundmusik (Apple Music, künftig Spotify, Podcasts, alle Apps mit respektvoller iOS-Audio-Session-Behandlung) systemseitig gedimmt, sobald ein Klangwelt-Sound spielt, und nach Sound-Ende wieder auf Originallautstärke gehoben. Die Dimm-Tiefe ist iOS-systemfest (~ -10 bis -15 dB, je nach iOS-Version) und nicht eigen-konfigurierbar.

Die alte `.mixWithOthers`-Option wird ersetzt durch `.duckOthers`. Hintergrund: `.mixWithOthers` legt den Klangwelt-Sound auf voller Lautstärke über die Musik, was nur trägt, wenn die Klangwelt-WAVs gegen die Musik anbrüllen können. Das ist eine Robustheits-Last, die wir uns mit `.duckOthers` ersparen — die WAVs müssen klar erkennbar und charaktervoll sein, aber nicht durchsetzungsstark gegen einen vollen Mix.

### Volume-Resolution

Die App bietet drei Lautstärke-Stufen (`SoundVolume.quiet`, `.normal`, `.loud`), seit Build 10727 logarithmisch gespreizt (0.15 / 0.5 / 1.0). Beim Abspielen jedes Sounds wird die effektive Lautstärke zur Laufzeit aufgelöst:

```swift
let resolvedVolume: Float
if userVolume == .quiet && AVAudioSession.sharedInstance().isOtherAudioPlaying {
    resolvedVolume = SoundVolume.normal.volumeLevel  // 0.5
} else {
    resolvedVolume = userVolume.volumeLevel
}
```

**Begründung:** `.quiet` ist semantisch eine Quiet-Office-Stufe — gedacht für Mandantengespräche, Bibliothek, konzentriertes Arbeiten ohne Musik. Wenn dennoch Hintergrundmusik läuft, ist die Quiet-Stufe nach Ducking praktisch unhörbar (Musik gedimmt, Sound ohnehin leise). In dieser Situation ist die Quiet-Stufe nicht der gemeinte Use-Case mehr, daher wird auf `.normal` skaliert. Der User hört seinen Klangwelt-Sound, ohne dass die Quiet-Setting fälschlicherweise verschluckt wird.

`.normal` und `.loud` werden nicht verschoben — sie sind durch das Ducking sowieso hörbar.

### Pre-Loading

Klangwelt-WAVs werden im SoundManager bei der ersten Anforderung pro `(world, action)`-Paar in `AVAudioPlayer`-Instanzen geladen und gecacht. Das vermeidet Latenz beim Abspielen häufig genutzter Sounds (`teilaufgabeErledigt`).

Optional zu prüfen: bei App-Start einen Pre-Warming-Pass über die Stamm-Sounds der gerade aktiven Klangwelt(en) durchführen, sodass auch die erste Subtask-Erledigung des Tages keine Datei-Lade-Latenz hat. Diese Optimierung ist nicht zwingend, aber sinnvoll. Entscheidung in der Code-Etappe zur Audio-Session-Robustheit.

### Player-Pool oder Single-Instance — Sound-Kaskaden

In der heutigen Architektur (`SoundManager.swift` Z. 208) ist `audioPlayer` eine einzelne Instanz-Variable, jeder neue `play()`-Aufruf überschreibt den vorherigen. Das ist ein offener Punkt aus der TodoList (Build 10728, B-S2 Sound-Kaskaden).

Zwei Optionen:

**Player-Pool:** Mehrere `AVAudioPlayer`-Instanzen, neue Sounds laufen parallel zu noch klingenden. Vorteil: Kaskaden möglich, kein abruptes Abreißen. Nachteil: Klangwelt kann bei schneller Trigger-Folge zur Klangwand werden — gegen Manifest-Prinzip Utility (Sound an signifikanten Momenten, nicht überall).

**Bewusster Abbruch:** Single-Instance bleibt, neue Sounds beenden laufende. Vorteil: jeder Trigger wird hörbar, keine Klangwand. Nachteil: Wenn der User schnell mehrere Subtasks in Folge abhakt, hört er den Sound für die ersten Subtasks teilweise abgebrochen.

**Empfehlung dieser Methodik: bewusster Abbruch.** Begründung: Im Tagesalltag mit häufigem Subtask-Abhaken liegt die Sound-Distanz typisch bei 1–3 Sekunden zwischen den Triggern, und die Sounds sind 0.5–1 Sekunde lang — Kaskaden wären selten nötig, aber wenn sie auftreten, sind sie Belastung. Ein klarer Abbruch ist UX-sauberer als überlappende Klänge.

Diese Empfehlung ist die Vorgabe für Build 10728. Falls die Experimentier-Phase (Sektion 7) zeigt, dass abrupte Abbrüche bei bestimmten Welten als unangenehm wahrgenommen werden, kann ein **gefadeter Abbruch** (kurzes Volume-Fade über ~50 ms) als Mittelweg implementiert werden — der schneidet nicht hart, aber lässt den nächsten Sound trotzdem sofort spielen.

---

## ElevenLabs-Workflow und Prompt-Schablone

Die Werkzeugkette zur Sound-Generierung. Diese Sektion hält die Setup-Regeln fest, die aus der Recherche zu ElevenLabs-Best-Practices stammen, und führt die 8-Slot-Schablone als verbindliches Format für jeden Prompt ein.

### Setup-Regeln

Vor jeder Generierungs-Sitzung in ElevenLabs sind folgende Einstellungen zu prüfen:

| Einstellung | Wert | Begründung |
|---|---|---|
| **Sprache** | Englisch | UI-Empfehlung von ElevenLabs; englische Prompts liefern messbar bessere Ergebnisse |
| **Loop** | aus | Klangwelt-Sounds sind Events, keine Ambient-Loops — beeinflusst die Generierung von Anfang/Ende |
| **Auto-Modus** | aus | Manuelle Kontrolle über Prompt und Dauer ist Voraussetzung |
| **Prompt-Influence** | ~30 % | Gibt ElevenLabs Spielraum für klangliche Qualität, ohne den Prompt zu ignorieren |
| **Dauer im UI** | hart in Sekunden gesetzt | Redundant zur Dauer-Angabe im Prompt — UI ist Zwang, Prompt ist Bitte |
| **Output-Format** | WAV 48 kHz | Höchste Qualität als Ausgangsmaterial; Konvertierung in App-Bundle-Format erst später |

**Zur Prompt-Längen-Praxis:** ElevenLabs generiert in der Beobachtung typischerweise 10–20 % kürzer als im Prompt spezifiziert. Wenn die klingende Soll-Dauer 1.5 Sekunden ist, sollte der Prompt eher 1.7–1.8 Sekunden ansagen. Das ist ein Korrekturfaktor, der in der Experimentier-Phase verifiziert wird.

### Generierungs-Pattern: 3–4 Varianten pro Prompt

Pro Aktion × Welt werden 3–4 Varianten generiert. Aus diesen wählt Chris die finale Version aus — typischerweise die, die am besten zu Charta und Klang-Klasse passt. Verworfene Varianten werden nicht aufbewahrt; die Bewertung in `MCK_Bewertung.xlsx` bezieht sich auf die ausgewählte Version.

### Die 8-Slot-Schablone

Jeder ElevenLabs-Prompt füllt acht Slots bewusst, statt einer freien Beschreibung. Das macht Prompts vergleichbar, auditierbar und systematisch verfeinerbar.

| Slot | Was er liefert | Beispiel (Salon, `hauptaufgabeKomplett`) |
|---|---|---|
| **1. Sound-Welt** | Instrumentierung, Klangfarbe — die Welt-Identität (aus der Charta) | *warm acoustic piano in an intimate room with small natural reverb* |
| **2. Tonalität** | Tonart, Akkordcharakter, Modalität | *C major, with a clear major-third resolution* |
| **3. Attack** | Wie der Sound einsetzt | *starting with a soft, deliberate piano note* |
| **4. Charakter** | Was in der Mitte passiert | *ascending three-note phrase rising into a fuller chord* |
| **5. Resolution** | Wie der Sound endet — *der entscheidende Slot* | *resolving on a settled major chord with gentle decay* |
| **6. Funktion** | Semantische Botschaft, was der Hörer fühlen soll | *like completing a meaningful task — satisfying, warm, accomplished* |
| **7. Dauer** | Hart, in Sekunden (mit Korrekturfaktor) | *exactly 2.0 seconds* (für klingende Soll-Dauer 1.7 s) |
| **8. Negative Constraints** *(optional)* | Was nicht passieren darf | *no reverb tail beyond 200 ms, no fade — clean cutoff at the end* |

**Wieso diese Slot-Reihenfolge wichtig ist:** Slots 1–2 verankern die Welt-Identität, Slots 3–5 beschreiben den klanglichen Verlauf in zeitlicher Reihenfolge, Slot 6 verankert die Funktion (kreuzt zur Klang-Klasse aus Sektion 4), Slot 7 ist hart-technisch, Slot 8 räumt Padding-Probleme aus dem Weg.

**Slot 5 ist der wichtigste.** Sehr viele Prompts beschreiben *was endet*, aber nicht *wie es endet*. Der Unterschied zwischen einem Sound, der *aufhört* und einem, der *abschließt*, liegt fast vollständig in diesem Slot. Bei den drei Tagesgesten (`tagBegonnen`, `tagKomplett`, `tagBeendet`) und bei `hauptaufgabeKomplett` ist die Resolution klanglich das Herz der Aktion — Skip- und Warn-Sounds dagegen lassen die Resolution bewusst offen oder abgehackt.

### Strategie-Wahl: Hybrid (Kernmotiv pro Welt + 8-Slot-Schablone)

Aus den Welt-Chartas: Salon, Cityflow und Horizont haben Kernmotiv-Vorschläge dokumentiert (Salon: aufsteigende 3-Ton-Phrase C-E-G; Cityflow: weicher Rhodes-Akkord Cmaj7 auf Lo-Fi-Beat; Horizont: Pad-Swell mit kristallinem Chime in F-Quinte). Diese Kernmotive sind nicht starre Kompositionen, sondern *Stilanker* — Bezugspunkte, an denen sich Prompts pro Aktion orientieren.

Praktisch heißt das:

- Das Kernmotiv erscheint im Slot 1 (Sound-Welt) als Stilreferenz, nicht als Komposition, die ElevenLabs reproduzieren soll.
- Pro Aktion variieren Slots 3–5 (Attack, Charakter, Resolution) das Motiv: `teilaufgabeErledigt` = nur Mittelton/Akzent aus dem Motiv; `hauptaufgabeKomplett` = ganzes Motiv mit voller Resolution; `teilaufgabeUebersprungen` = Motiv abgebrochen vor Resolution; `aufgabeFaellig` = Motiv aufmerksamkeitsweisend, ohne Resolutions-Anspruch.
- Innerhalb einer Welt entsteht so eine *Familien-Kohärenz*, ohne dass ElevenLabs motivische Gleichheit reproduzieren muss (das wäre über die Tool-Limitierung hinaus).

Welten ohne dokumentiertes Kernmotiv (Morgenwald, System) folgen ihrer Charta-Logik direkt: Morgenwald über Naturmarker (mindestens ein Vogel/Blatt/Wasser-Element pro Sound), System über Harmonien und Klangcharakter (siehe Charta).

### Sound-Verdienst meets Prompt: Klasse C als Sonderfall

Klasse-C-Sounds (`.aktionRegistriert`, `.aktionAbgeschlossen`, `.warnungVorAktion`) werden pro Welt einmal geprompt — kontext-frei. Die Welt-Quelle entscheidet zur Laufzeit, in welcher Welt der Sound spielt; der Sound selbst ist innerhalb seiner Welt einheitlich.

Die Mapping-Logik Klasse C → Slot-Belegung:

- `.aktionRegistriert` (Level 1 subtil): Slots 3–5 sind kurz, knapp, Resolution gedämpft. Slot 7 = 0.4–0.7 s. Slot 6 = *„like a quiet acknowledgment, registered but not celebrated"*.
- `.aktionAbgeschlossen` (Level 2 Erfolgsmeldung): Slots 3–5 mit voller Resolution-Geste, Slot 4 darf eine Phrase tragen. Slot 7 = 1.5–2.5 s. Slot 6 = *„like a satisfying completion of a meaningful action"*.
- `.warnungVorAktion`: Slots 3–4 etablieren Spannung, Slot 5 lässt die Resolution explizit offen oder leicht dissonant. Slot 7 = 0.8–1.5 s. Slot 6 = *„like a question that deserves attention before answering"*.

---

## Iterations-Methodik und Experimentier-Phase

Bevor 80 WAV-Sätze produziert werden, verifizieren wir die Methodik selbst an wenigen Test-Sounds. Diese Phase liefert drei Resultate: empirisch belegte Bänder für Sound-Längen, justierte Prompt-Formulierungs-Praxis und eine kalibrierte Erwartung an ElevenLabs-Output.

### Kollaborations-Workflow

Diese Phase wird gemeinsam zwischen Chris und Claude durchgeführt:

- **Claude** schreibt Prompts gemäß 8-Slot-Schablone, schlägt Variationen vor, dokumentiert Erwartungen pro Experiment.
- **Chris** generiert die Prompts in ElevenLabs (3–4 Varianten je Prompt), hört die Ergebnisse am Test-Setup, gibt Rückmeldung zu Treffer, Schwächen und Auffälligkeiten.
- **Gemeinsam** wird ausgewertet: Was bestätigt sich an der Methodik, was muss verfeinert werden, welche Bänder verschieben sich.

Rückmeldungen kommen typischerweise als Audio-Datei plus kurze schriftliche Beobachtung (zu lang, zu dünn, Welt-Identität trifft, Resolution kommt nicht durch, Stille am Ende, etc.). Claude pflegt die daraus folgenden Erkenntnisse in `MCK_Bewertung.xlsx` ein und passt die Methodik dieser Datei an, falls sich strukturelle Korrekturen ergeben.

### Experimente

Folgende Experimente werden in der Phase durchgeführt. Sie sind nicht alle nötig; die Reihenfolge ist priorisiert. Welche tatsächlich gemacht werden, entscheiden wir gemeinsam nach den Ergebnissen der ersten zwei.

**Experiment 1 — Längen-Verifikation an einer Aktion**

Welt: Salon (bekanntes Vokabular, gut zu beurteilen). Aktion: `teilaufgabeErledigt`. Drei Prompt-Varianten mit identischen Slots 1–6, 8 — variiert wird nur Slot 7 (Dauer): 0.5 s, 0.7 s, 1.0 s. Frage: Welche Länge fühlt sich beim Subtask-Abhaken am rechtesten an? Verschieben sich die Bänder?

**Experiment 2 — Klingende vs. technische Dauer**

Selber Prompt wie Experiment 1, eine Variante. Frage: Wie viel Stille hängt ElevenLabs typisch hinten dran, wie viel vorne? Bestätigt sich der 10–20 %-Korrekturfaktor in der Praxis? Entscheidet, ob wir die Soll-Längen im Prompt nach oben anpassen müssen.

**Experiment 3 — Slot-Wirksamkeit (Resolution)**

Eine Aktion (`hauptaufgabeKomplett`, Welt Salon) in zwei Prompt-Varianten: einmal mit explizitem Slot 5 („*resolving on a settled major chord with gentle decay*"), einmal mit weichem Schluss ohne Resolution-Beschreibung. Frage: Wie deutlich ist der wahrgenommene Unterschied? Lohnt der Slot seinen Aufwand?

**Experiment 4 — Prompt-Detailtiefe**

Selbe Aktion (`tagBegonnen`, Welt Morgenwald) in drei Detailtiefen: minimal (nur Slots 1, 5, 7), mittel (alle 7 Pflichtslots), üppig (alle 8 Slots inklusive ausführlicher Negative Constraints). Frage: Bei welcher Tiefe kippt ElevenLabs zwischen „folgt der Anleitung" und „verliert sich in Details"? Wo liegt das Sweet-Spot?

**Experiment 5 — Welt-Identitätsschärfe**

Dieselbe Aktion (`teilaufgabeErledigt`) in zwei Welten parallel — Salon und Horizont. Frage: Wird die Welt-Identität klar wahrgenommen, oder klingen die Sounds verwandt? Falls zu wenig Charakter-Differenzierung: justieren wir die Charta-Sprache, oder verstärken wir die Slot-1-Beschreibungen?

**Experiment 6 — Robustheits-Test (am Gerät, nach Code-Etappe)**

Setzt `.duckOthers` und Volume-Resolution voraus (Code-Etappe Audio-Session-Robustheit). Generierte Klangwelt-Sounds parallel zu laufender Apple-Music am echten iPhone testen — kommen sie durch? Ist das Ducking angemessen? Ist die Volume-Quiet-Eskalation auf Normal bei Hintergrundmusik wahrnehmbar verbessert?

**Experiment 7 — System-Welt im Vergleich zu iOS-System-IDs**

Setzt erste System-Welt-Sounds voraus. Diese werden A/B gegen die heutigen iOS-System-Sound-IDs gehört (1001 / 1054 / 1307 etc.). Frage: Erfüllen unsere System-WAVs die Robustheits-Anforderung der Charta — sharp transients, mid-frequency focus, definiertes Hüll-Ende, perceptuell ausgeglichene Lautheit?

### Welt-Reihenfolge bei der Produktion

Nach der Experimentier-Phase beginnt die WAV-Produktion. Reihenfolge orientiert sich an Lernkurve und Komplexität:

1. **Salon** — bekanntes Welt-Vokabular, klare Charta, dokumentiertes Kernmotiv. Erste vollständige Welt schärft die Methodik weiter.
2. **Morgenwald** — auditory icons, andere Sound-Design-Schule. Verifiziert, dass die Methodik nicht nur für Earcons funktioniert.
3. **Cityflow** — Hybrid-Welt mit dokumentiertem Schwachpunkt (Tagesabschluss-Resolution fehlt im Lo-Fi-Vokabular). Methodik bewährt sich an einem schwierigen Fall.
4. **Horizont** — synthetisch, Earcons in völlig anderem Idiom als Salon. Sollte schnell laufen, weil die Methodik dann eingespielt ist.
5. **System** — eigene Sound-Design-Schule (technisch-harmonisch ohne melodische Phrasen), neue Designstrategie C mit Rückfall B. Komplexester Fall, daher zuletzt — Methodik dann bestens kalibriert.

Diese Reihenfolge ist ein Vorschlag, kein Gesetz. Wenn sich in den Experimenten zeigt, dass eine andere Reihenfolge sinnvoller ist (etwa System früher, weil sie strukturell Klarheit für die anderen Welten schafft), justieren wir.

### Cluster-Reihenfolge innerhalb einer Welt

Wenn eine Welt produziert wird, gehen wir die 16 Aktionen in dieser Reihenfolge durch:

1. **Aufgabenbezug zuerst** — `teilaufgabeErledigt`, `teilaufgabeUebersprungen`, `hauptaufgabeKomplett`, `ueberfaelligErledigt`, `aufgabeFaellig`, `aufgabeUeberfaellig`, `aufgabeVerfallen`. Das sind die kategoriebezogenen Sounds und der Härtetest des Welt-Vokabulars: in den kurzen, häufig gehörten Sounds zeigt sich, ob die Charta-Sprache präzise genug ist.
2. **Tagesbezug danach** — `tagBegonnen`, `tagKomplett`, `tagBeendet`. Die ritualisierten Klammern. Wenn der Akzent aus Schritt 1 sitzt, skaliert sich das Vokabular natürlich nach oben — größere Resolution, längerer Verlauf, gleiches Idiom.
3. **Rest zum Schluss** — `.updateVerfuegbar`, `.syncCompleted`, `.syncFailed`, `.aktionRegistriert`, `.aktionAbgeschlossen`, `.warnungVorAktion`. Klasse-B-System-Sounds und Klasse-C-Synthesen. Die Klasse-C-Sounds sind generalisiert und funktionieren erst, wenn das Welt-Vokabular geklärt ist — sie verwenden das etablierte Idiom als Material.

Diese Reihenfolge folgt der Welt-Quellen-Logik aus Sektion 4: Aufgabenbezug ist Kategorie-Welt, Tagesbezug und Rest sind Standard-Klangwelt. Konzeptionell elegant, weil die Welt im häufigeren Pfad zuerst kalibriert wird.

### Bewertungs-Praxis

Pro Aktion × Welt wird die ausgewählte Variante in [`MCK_Bewertung.xlsx`](MCK_Bewertung.xlsx) eingetragen. Die Tabelle ist die kanonische Bewertungs-Quelle und folgt einer 6-stufigen Skala:

| Wert | Bedeutung | Hinweis |
|---|---|---|
| 0 | Ton fehlt | Datei nicht vorhanden — Prompt nötig |
| 1 | Ton ist ungeeignet | Klangwelt-fremd oder qualitativ schlecht — neu generieren |
| 2 | Ton ist für das Ereignis ungeeignet | Klingt OK, passt aber nicht zum Ereignis |
| 3 | Ton ist OK | Brauchbar, könnte aber besser sein |
| 4 | Ton ist gut | Stimmig, kann bleiben |
| 5 | Ton muss bleiben! | Hervorragend, nicht ersetzen |

Die Spalten der Tabelle sind: Klangwelt, Gruppe, RawValue, Anzeige, Stamm?, Erwarteter Dateiname, Datei vorhanden?, Bewertung, Bewertungs-Text (Formel aus Bewertung), Kommentar, Neuer Prompt nötig?

**Schwellen-Logik für die Produktion:**

- Bewertung **3+** ist der Mindeststandard für die App-Bundle-Aufnahme. Sounds darunter werden nicht ausgeliefert, sondern neu generiert.
- Bewertung **4–5** ist das Produktions-Ziel. Eine Welt gilt als „fertig", wenn alle 16 Aktionen mindestens auf 4 stehen.
- Bewertung **5** ist die seltene Auszeichnung — sie signalisiert „nicht ersetzen, auch nicht bei späteren Iterationen".

Verworfene Varianten werden nicht aufbewahrt; die Bewertungs-Tabelle bezieht sich ausschließlich auf die im Bundle eingesetzte Version. Falls eine Variante als „akzeptabel, aber nicht ideal" eingestuft wird (3), notiert der Kommentar den Sanierungsbedarf — sie bleibt im Bundle, kommt aber in eine zukünftige Re-Generierungs-Welle.

> **Hinweis zur XLSX-Struktur:** Die heutige Tabelle spiegelt die alte 17-Aktionen-Architektur mit Fallback-Logik wider. Mit der Konsolidierung auf 16 Aktionen ohne Fallbacks und der Aufwertung der System-Welt zu eigenen WAVs (Manifest, Charta) muss die Tabelle umgebaut werden — neue Zeilen für die Klasse-C- und Klasse-B-System-Aktionen, alte Administration- und Doppelvarianten-Zeilen raus. Diese Restrukturierung ist eine eigene Folge-Etappe, dokumentiert in der TodoList.

### Übergang Experimentier-Phase → Produktion

Wenn die Experimente 1–5 zufriedenstellend abgeschlossen sind und die Bänder, Korrekturfaktoren und Schreib-Konventionen kalibriert sind, beginnt die produktive Prompt-Schreibung in `MCK_Prompts.md`, Welt für Welt nach obiger Reihenfolge. Experimente 6 und 7 laufen parallel zur Produktion und greifen ggf. zurück, wenn Robustheits-Probleme auftauchen.

Diese Methodik wird im Verlauf der Produktion ergänzt und justiert. Erkenntnisse aus den Experimenten und der Produktion gehen direkt in das nächste Kapitel-Update zurück — die Methodik ist nicht statisch, sondern wächst mit der Erfahrung.

---

## Cluster A — Dirty-Flag-Logik im Edit-Sheet

Eine spezielle technische Frage aus der Sound-Verdienst-Diskussion: Cluster-A-Trigger (Edit-Sheet schließt mit Änderung) löst `.aktionRegistriert` aus — aber *nur*, wenn tatsächlich eine Änderung stattgefunden hat. Bei `@Bindable` mit SwiftData-Auto-Save persistiert jeder Tastendruck sofort, es gibt keinen klaren „Save"-Moment, und ein bloßer Sheet-Open-Close ohne Eingabe darf keinen Sound auslösen.

### Zwei Implementierungs-Wege

**Snapshot + Diff:** Beim `.onAppear` des Sheets wird ein Snapshot der relevanten Felder genommen. Beim `.onDisappear` wird gegen den aktuellen Stand verglichen. Wenn Differenz, spielt der Sound.

**Dirty-Flag:** Eine `@State`-Variable `isDirty: Bool` wird auf `false` initialisiert. Jede Eingabe-Komponente (TextField, Picker, Toggle) bindet ihren Wert über einen Wrapper, der bei jedem Schreibvorgang `isDirty = true` setzt. Beim `.onDisappear` wird das Flag ausgewertet.

### Empfehlung — Dirty-Flag

Begründung:

- **Performanter** — kein redundantes Snapshot-Halten und kein Field-für-Field-Vergleich.
- **Einfacher zu implementieren** — keine Reflektion über Datenmodell-Strukturen, kein Equatable-Aufwand für SwiftData-Objekte.
- **Semantisch passender** — die Frage ist *„hat der User etwas eingegeben?"*, nicht *„hat sich ein Datenwert geändert?"*. Wenn der User einen Titel ändert und wieder zurückschreibt, ist trotzdem eine Eingabe erfolgt — Dirty-Flag erkennt das, Snapshot+Diff nicht.

### Implementierungs-Skizze

```swift
struct EditTaskView: View {
    @Bindable var task: MainTask
    @State private var isDirty: Bool = false
    @Environment(\.dismiss) private var dismiss

    var body: some View {
        Form {
            TextField("Titel", text: Binding(
                get: { task.title },
                set: { task.title = $0; isDirty = true }
            ))
            // ... weitere Felder mit demselben Wrapper-Pattern
        }
        .onDisappear {
            if isDirty {
                SoundManager.shared.play(.aktionRegistriert,
                                         world: task.category?.soundWorld ?? defaultSoundWorld)
            }
        }
    }
}
```

Pattern-Wiederverwendung: derselbe Mechanismus greift in `EditTemplateView` und `EditCategoryView` mit den jeweils relevanten Feldern. Ein dedizierter `Binding`-Helper kann das wiederholte Set-Wrapping vereinfachen.

### Edge-Case: Programmatische Änderungen

Wenn die App selbst (nicht der User) das Datenmodell ändert während das Sheet offen ist — z. B. eine eingehende CloudKit-Sync-Aktualisierung, die Felder von außen modifiziert — würde das den Dirty-Flag nicht setzen, weil die Setter-Wrapper nicht durchlaufen werden. Das ist gewünscht: kein Sound für externe Änderungen, nur für User-Eingaben.

---

## Haptik

Haptisches Feedback ist heute 1:1 an den Sound-Aufruf im SoundManager gekoppelt — `triggerHaptic(for:isHighPriority:)` wird aus `play(action:world:isHighPriority:)` immer mitgerufen, sofern `hapticEnabled == true` (auch bei `isEnabled == false` für Sound). Diese Methodik betrachtet Haptik vorerst als **Annex der Klangwelt**, nicht als eigene Säule.

### Zuordnung pro Klang-Klasse

| Klasse | Haptik-Variante | Begründung |
|---|---|---|
| **A — Workflow-Vollendung** (`teilaufgabeErledigt`, `hauptaufgabeKomplett`, `tagBegonnen`, `tagBeendet`, `ueberfaelligErledigt`) | `UINotificationFeedbackGenerator(.success)` | Vollendungs-Geste, eindeutig positiv |
| **A — Workflow-Skip** (`teilaufgabeUebersprungen`) | `UIImpactFeedbackGenerator(.light)` | Neutral, ohne emotionale Wertung |
| **A — Tagesfortschritt 100 %** (`tagKomplett`) | `UINotificationFeedbackGenerator(.success)` zweifach mit 0.3 s Versatz (analog zum Doppelton) | Größter Tagesmoment, doppelt markiert |
| **B — Aufgaben-Ereignis** (`aufgabeFaellig`, `aufgabeUeberfaellig`, `aufgabeVerfallen`) | `UINotificationFeedbackGenerator(.warning)` | Aufmerksamkeit, ohne dass eine User-Aktion erfolgt |
| **B — App-System** (`.updateVerfuegbar`, `.syncCompleted`, `.syncFailed`) | `UIImpactFeedbackGenerator(.light)` für Hinweise, `.warning` für `.syncFailed` | Differenzierung System-routine vs. System-Fehler |
| **C — Bestätigung Level 1** (`.aktionRegistriert`) | `UIImpactFeedbackGenerator(.light)` | Subtil, nicht aufdringlich |
| **C — Bestätigung Level 2** (`.aktionAbgeschlossen`) | `UINotificationFeedbackGenerator(.success)` | Erfolgsmeldung mit Gewicht |
| **C — Warnung** (`.warnungVorAktion`) | `UINotificationFeedbackGenerator(.warning)` | Konsistent mit Klasse-B-Aufmerksamkeit |

Diese Tabelle ersetzt die heutige 16-case-Implementierung in `SoundManager.swift` Z. 461–540. Mit der Aktions-Konsolidierung von 17 auf 16 Aktionen wird die Mapping-Tabelle gleichzeitig schlanker.

### Haptik-Toggle-Verhalten

Die Settings-Variable `hapticEnabled` greift unabhängig von `isEnabled` (Sound) — d. h., der User kann Haptik aktiv haben, auch wenn Sound deaktiviert ist (etwa im Mandantengespräch ohne Klang). In der Sound-Verdienst-Diskussion (Inventur Sektion 5) wurde explizit festgehalten: Toggle „Haptisches Feedback" aktivieren spielt keinen Sound, sondern triggert nur eine Haptik-Probe als Bestätigung der Aktivierung — vermutlich `.light Impact`.

### Offene Frage: Eigene Säule?

Die Frage, ob Haptik perspektivisch eine eigene Säule mit Manifest, Methodik und Inventur verdient, ist offen. Heute ist die Mapping-Komplexität klein genug, um sie als Annex der Klangwelt zu führen. Bei Erweiterungen (etwa unterschiedliche Haptik-Profile pro Welt, eigenständige Haptik-Designs unabhängig vom Sound, Accessibility-spezifische Haptik-Layer) würde die Säulen-Frage neu zu bewerten sein.

Diese Frage steht als TODO in der Pflege-Liste: **„Haptik-Säulen-Frage auf Sinnhaftigkeit überprüfen, Zeitpunkt offen."** Wird in der TodoList aufgenommen.

---

## Verweise

- [`MCK_Manifest.md`](MCK_Manifest.md) — Designprinzipien und Negativliste, Bezugspunkt für jede Methodik-Entscheidung
- [`MCK_Chartas.md`](MCK_Chartas.md) — die fünf aktiven Welt-Profile mit Klangmaterial, Tonalität und Kernmotiv
- [`MCK_Inventur.md`](MCK_Inventur.md) — Code-Aufruf-Inventur mit Sound-Verdienst-Beschluss in Sektion 5
- [`MCK_Dateischema.md`](MCK_Dateischema.md) — WAV-Dateischema und Bundle-Inventar
- [`MCK_Prompts.md`](MCK_Prompts.md) — die konkreten ElevenLabs-Prompts pro Aktion × Welt
- [`MCK_Bewertung.xlsx`](MCK_Bewertung.xlsx) — Bewertungsmatrix der ausgewählten WAVs
- [`MyCadenza_Gestaltung.md`](../MyCadenza_Gestaltung.md) — übergreifende Designprinzipien Klang ↔ Visuell
