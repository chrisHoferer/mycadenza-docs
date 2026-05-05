# MCK · Manifest der Klangwelt

> Identität, Prinzipien und Grenzen der akustischen Gestaltung von MyCadenza.
> Stand: 05.05.2026 · Status: Erstfassung

---

## Wozu eine Klangwelt?

MyCadenza ist eine Tagesplaner-App. Klang ist hier kein Selbstzweck und kein Stilelement — Klang ist eine Bestätigungs- und Orientierungsschicht über dem visuellen Geschehen. Wenn der Nutzer eine Aufgabe abhakt, eine Hauptaufgabe vollendet, einen Tag beginnt oder beendet, soll ein passender Klang die Geste bekräftigen, ohne sie zu überdecken. Die Klangwelt ergänzt die App, sie ist nicht ihr Hauptmerkmal.

Aus dieser Position folgen drei Verpflichtungen, die jede Klanggestaltung in MyCadenza bindet.

## Drei Designprinzipien

### Causality — Klang folgt einer erkennbaren Aktion

Jeder Klang in MyCadenza muss eine erkennbare Ursache haben. Der Nutzer soll im Moment des Klangs verstehen, *warum* er klingt. Das schließt drei Klassen aus:

- **Klang ohne Auslöser** — Hintergrundambient, atmosphärische Schleifen, „Begrüßungsmelodien" beim App-Start. Hintergrundmusik ist Sache der Musik-Integrationen (Apple Music, künftig Spotify), nicht der Klangwelt. Beide Schichten existieren parallel: Die Klangwelt bleibt zuständig für Ereignis- und Erfolgsklänge auch dann, wenn Hintergrundmusik läuft — die App-Begleitung muss durchdringen.
- **Klang mit nicht erkennbarer Ursache** — wenn der Nutzer den Klang nicht innerhalb von Sekunden mit einer Aktion oder einem Ereignis verknüpfen kann, ist der Klang verwirrend statt informativ.
- **Klang als reine Verzierung** — wenn ein Klang nichts kommuniziert, was die Visualisierung nicht schon kommuniziert, ist Stille die bessere Wahl.

### Harmony — Klang, Visuelles und Haptik passen zueinander

Wenn ein Status visuell grün und vollendet wirkt, soll der zugehörige Klang eine Vollendungsgeste sein, kein Mahnungston. Wenn ein Element visuell zur Aufmerksamkeit auffordert, darf der Klang nicht beruhigend sein. Diese cross-modale Stimmigkeit ist die Brücke zwischen `MCK_Manifest.md` und `MCD_Manifest.md` — sie wird übergreifend in `MyCadenza_Gestaltung.md` festgehalten und in beiden Welten respektiert.

Konkret bedeutet das:

- Vollendung visualisiert in Farbe X → Klang setzt eine tonale Auflösung, keine Spannung
- Warnung visualisiert in Farbe Y → Klang ist eindringlicher als ein neutrales Feedback
- Information visualisiert in dezentem Grau → Klang ist gar nicht da, oder ein leichtes Tippen ohne tonalen Anspruch

### Utility — Klang an signifikanten Momenten, nicht überall

Apple's Human Interface Guidelines sagen es deutlich: *„It is often a good idea not to add sound. Start by identifying possible locations for audio feedback, then focus only on the elements where it can enhance the experience."* MyCadenza folgt diesem Prinzip strikt. Bei einem Tag mit zwanzig Teilaufgaben würde jeder Mikro-Klang zur Belastung — also bekommen nur die Momente einen Klang, an denen er etwas leistet, was das Auge nicht leistet:

- Bestätigung einer abgeschlossenen Geste (Teilaufgabe, Hauptaufgabe, Tag)
- Kontextwechsel von Bedeutung (Tag begonnen, Tag beendet)
- Aufmerksamkeit für ein Ereignis ohne Userinteraktion (Aufgabe fällig, überfällig, verfallen)
- Bestätigung einer administrativen Operation, die der Nutzer ausgelöst hat (Erstellt, Gelöscht, Zurückgesetzt)
- Quittierung einer App-internen Vollendung (Sync abgeschlossen, Sync fehlgeschlagen)

Alle anderen Trigger sind bewusst stumm — Sheet-Öffnen, Reorder, Tab-Wechsel, Validierungsfehler bei Disabled-Buttons, App-Start, Update-Anzeigen.

## Sound-Weight — Klanggewicht passt zur Bedeutung

Aus Utility folgt direkt, dass Klanggewicht und Bedeutung der Aktion übereinstimmen müssen. Eine Teilaufgabe ist klein — ihr Klang ist kurz und leicht. Eine Hauptaufgabe komplett ist groß — ihr Klang darf länger sein, kann eine Phrase tragen, darf eine Resolution vollführen. Ein Tagesabschluss ist die größte Geste im täglichen Workflow — ihr Klang trägt die meiste Substanz.

Dieses Prinzip strukturiert die Längen-Konvention in `MCK_Methodik.md` und ist der Grund, warum administrative Operationen (Erstellt, Gelöscht) klanglich *kleiner* sein müssen als Workflow-Vollendungen (Hauptaufgabe komplett) — auch wenn sie in der Codebasis gleich oft vorkommen.

## Was die Klangwelt nicht ist

Diese Negativliste schützt die Identität. Wenn einer der Punkte hier verletzt wird, ist es kein Klangwelt-Problem mehr, sondern ein Gestaltungsfehler.

- **Keine Hintergrundmusik.** Klangwelten sind Event-Klänge, keine atmosphärischen Loops. Hintergrundmusik kommt aus der Apple-Music-Integration und steht klanglich neben der Klangwelt, nicht in ihr.
- **Keine Sprachausgabe.** Die App spricht nicht, sie klingt. Ansagen, Voiceover, gesprochene Bestätigungen sind out of scope — Accessibility wird über VoiceOver-Labels gelöst, nicht über eigene Audioausgabe.
- **Kein Lärm bei Mikrointeraktionen.** Tippen in Textfeldern, Scrollen, Pull-to-Refresh, Tab-Wechsel und Hover-Effekte sind klanglich tabu, auch wenn sie technisch Trigger sein könnten.
- **Keine Klangwelt ohne Designentscheidung.** Eine Klangwelt entsteht nicht, indem man pro Aktion einen Sound generiert. Sie entsteht aus einer Sound-Identität (Was ist die Welt klanglich?) und einer Sound-Funktion (Was leistet der Klang in der Aktion?). Ohne diese zwei Bezugspunkte ist es kein Klangwelt-Sound, sondern ein zufälliger Effekt.

## Familienstruktur

MyCadenza unterhält **eine technische System-Klangwelt** und **vier ästhetische Klangwelten**, plus einen dokumentierten Backlog für künftige Erweiterungen.

| Welt | Status | Sound-Typ | Funktion |
|---|---|---|---|
| System | aktiv | Earcons (technisch-harmonisch) | UI-nahe technische Klänge mit eigener Handschrift |
| Morgenwald | aktiv | Auditory icons (Naturklänge) | Naturnähe, Beruhigung |
| Salon | aktiv | Earcons (akustisch) | Klassisch-intim, präzise |
| Cityflow | aktiv | Hybrid (Lo-Fi-Ambient + Beats) | Urban, gemütlich, modern |
| Horizont | aktiv | Earcons (synthetisch) | Weit, ätherisch, ambient |
| Stille | Backlog | Auditory icons (minimal) | Sensible Kontexte (Mandantengespräch, Bibliothek) |
| Atelier | Backlog | Earcons (Sax-Lead) | Ausdrucksstark, abendlich-intim |
| Bluesroom | Backlog | Earcons (Jazz-Ensemble) | Erdig, swingend, mit Attitüde |
| Pavillon | Backlog | Earcons (asiatisch-meditativ) | Pentatonisch, ruhig, kontemplativ |

Die ausführlichen Welt-Profile stehen in `MCK_Chartas.md`. Die Aufnahme einer Backlog-Welt in den aktiven Bestand setzt eine eigene Designentscheidung voraus — keine Welt entsteht, weil ein Cluster „nett wäre".

## Beziehung zu anderen Säulen

Dieses Manifest ist die *Warum-Schicht* der Klangwelt. Die anderen drei Säulen folgen dieser logischen Kette:

- **MCK_Chartas.md** — pro Welt: Was ist die klangliche Identität? *(Was)*
- **MCK_Methodik.md** — Wie entwerfen wir konkrete Sounds? Welche Werkzeuge, Schablonen, Prozesse? *(Wie)*
- **MCK_Inventur.md** + **MCK_Dateischema.md** + **MCK_Bewertung.xlsx** — Was ist im Code und im Bundle? *(Was-ist-da)*
- **MCK_Prompts.md** — die konkreten ElevenLabs-Prompts pro Aktion × Welt *(Womit)*

Wer eine Designentscheidung trifft, prüft sie gegen das Manifest. Wer einen Sound generiert, prüft ihn gegen die Charta der Welt und die Methodik. Wer die Inventur ändert, ändert sie nicht aufgrund von Geschmacksfragen, sondern aufgrund von Designentscheidungen, die sich auf das Manifest stützen.

## Was sich in diesem Manifest *nicht* ändert

Das Manifest ist die stabilste Säule. Konkrete Prompts ändern sich oft, Methodik wird verfeinert, Welten kommen hinzu — aber Causality, Harmony, Utility, Sound-Weight und die Negativliste oben sind die Designkonstanten. Sie ändern sich nur dann, wenn die App selbst grundsätzlich neu gedacht wird (etwa durch Multi-User-Funktionalität, durch Wechsel der Plattform, durch eine andere Vision von „was ein Tagesplaner ist").

Solange MyCadenza ein Solo-Tagesplaner für iOS bleibt, gelten diese Prinzipien.

---

**Verweise:**
- [`MyCadenza_Gestaltung.md`](../MyCadenza_Gestaltung.md) — übergreifende Designprinzipien Klang ↔ Visuell
- [`MCK_Chartas.md`](MCK_Chartas.md) — die vier aktiven Welt-Profile
- [`MCK_Methodik.md`](MCK_Methodik.md) — Werkzeuge, Schablonen, Prozesse
- [`MCK_Inventur.md`](MCK_Inventur.md) — Code-Aufruf-Inventur
- [`MCK_Dateischema.md`](MCK_Dateischema.md) — WAV-Dateischema und Bundle-Inventar
