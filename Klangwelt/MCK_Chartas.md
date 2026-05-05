# MCK · Chartas der Klangwelten

> Klangliche Identität pro Welt — Charakter, Klangmaterial, tonale Sprache, Grenzen.
> Stand: 05.05.2026 · Status: Erstfassung
>
> Dieses Dokument folgt dem Manifest ([`MCK_Manifest.md`](MCK_Manifest.md)) und ist die Grundlage für die Methodik ([`MCK_Methodik.md`](MCK_Methodik.md)) und die konkreten Prompts ([`MCK_Prompts.md`](MCK_Prompts.md)).

---

## Aktive Klangwelten

Die fünf Welten, die in der App tatsächlich angeboten werden und für die WAV-Sets gepflegt werden.

### System

Eine technisch-harmonische Welt, die an das iPhone-Vokabular anknüpft, ohne mit iOS-Klängen verwechselbar zu sein. Eigene WAV-Bibliothek (analog zu den anderen vier Welten), aber mit konsequent technischem Klangcharakter: digitale UI-Sounds, synthetische Pings und Bells, gefilterte Synth-Klicks, Transient-orientierte Tones.

Die Welt unterscheidet sich von Salon und Atelier kategorisch in der Tonsprache: keine melodischen Phrasen, keine Tonfolgen, keine Liedkurven. Differenzierung zwischen Aktionen erfolgt über *Harmonie* (Dur-Terz, Moll-Terz, offene Quinte, Tritonus) und *Klangcharakter* (Helligkeit, Obertonreichtum, Filter), nicht über Bewegung. Damit ist System eine eigene Sound-Design-Schule, keine reduzierte Variante von Salon.

Atmosphärisch verortet im professionellen, konzentrierten oder iPhone-nahen Nutzungskontext: Arbeit, Mandantengespräch (in Kombination mit niedriger Lautstärke), Konzentrationsphasen, in denen die App akustisch unauffällig aber klar bleiben soll. Die Identität bleibt erkennbar, solange die Sounds *technisch* klingen (nicht akustisch-instrumental, nicht naturreferenziell, nicht atmosphärisch-musikalisch) und ihre Differenzierung harmonisch löst.

| | |
|---|---|
| **Sound-Typ** | Earcons (technisch-harmonisch) |
| **Klangmaterial** | Digitale UI-Sounds, synthetische Pings und Bells, gefilterte Synth-Klicks, Transient-orientierte Tones |
| **Tonalität** | Harmonien ohne melodische Phrasen — Dur-Terz / Moll-Terz / offene Quinte / sus2 / Tritonus als Stimmungsträger |
| **Frequenzfokus** | 1–4 kHz Hauptfokus (iOS-Lautsprecher-optimiert), keine signifikanten Tieffrequenz-Layer |
| **Identitätsmerkmal** | Sharp immediate attack, no soft onset, no reverb tail, clean cutoff — Differenzierung über Harmonie und Klangcharakter, nicht über Tonfolgen |
| **Charakter** | Sachlich, präzise, technisch-klar, durchsetzungsstark, professionell |
| **Kernreferenz** | „iPhone-Vokabular mit eigener Handschrift — wie ein gut gemachtes Custom-Theme im selben Sprachraum" |
| **Designstrategie** | C — Differenzierung großer Aktionen (Tag begonnen / beendet / Hauptaufgabe komplett) über Klangcharakter (Helligkeit, Obertonreichtum, Filter) bei harmonischer Tonsprache. **Rückfallpfad B:** Falls C bei diesen Aktionen nicht hörbar trägt, sind kurze 2-Ton-Akzente (z. B. Tonika-Quinte-Sprung) als Ausnahme erlaubt — keine Phrasen, nur minimale Bewegungs-Geste |
| **Robustheits-Anforderung** | Eigene WAVs müssen mindestens so durchsetzungsstark wahrnehmbar sein wie Apple-System-IDs — sharp transients, mid-frequency focus, definiertes Hüll-Ende, perceptuell ausgeglichene Lautheit über das Set |

### Morgenwald 🌿

Eine Welt aus erlebter Natur — keine Musik, sondern aufgenommene oder modellierte Naturklänge: Vogelstimmen, Blätterrauschen im Wind, fließendes Wasser, Insektenchöre in der Dämmerung. Die Welt arbeitet ausschließlich mit *auditory icons* — jeder Klang hat einen direkten Bezug zur realen Welt, das macht sie selbsterklärend und über alle Aktionen hinweg robust.

Atmosphärisch verortet morgens bis späteren Nachmittag, outdoor, lebendig. Sie eignet sich besonders für Nutzer, die Sound als beruhigenden Hintergrund schätzen, nicht als musikalische Aussage. Die Identität bleibt erkennbar, solange wenigstens *ein* Naturmarker (Vogel, Blatt, Wasser) hörbar ist; ein reines Klick-Geräusch wäre Identitätsverlust.

| | |
|---|---|
| **Sound-Typ** | Auditory icons (Naturklänge) |
| **Klangmaterial** | Vögel (Sing-/Ruf-/Chorklänge), Blätter, Wind, Wasser (Tropfen, Bach, Regen), Insekten dämmerlich |
| **Tonalität** | Nicht musikalisch tonal — natürliche Frequenzspektren |
| **Frequenzfokus** | Mittel bis hoch (1–6 kHz für Vögel), tiefe Layer für Wind/Wasser |
| **Identitätsmerkmal** | Mindestens ein erkennbarer Naturmarker pro Sound |
| **Charakter** | Warm, lebendig, beruhigend, unaufdringlich, real |
| **Kernreferenz** | „Lichtung am frühen Morgen, Vögel, leichte Brise" |

### Salon 🎹

Eine intim-akustische Welt: warmes Klavier (akustisch oder weich verstärkt), gelegentlich Akustikgitarre, dezente Streicher in Kammerbesetzung. Reines *earcon*-System — die Sounds sind musikalisch-symbolisch, ohne Naturreferenz, und tragen Bedeutung über tonale Gesten (aufsteigende Phrasen, Auflösungen, Mollwendungen).

Atmosphärisch verortet: Wohnzimmer mit gedämpfter Akustik, Bibliothek, kleines Konzertstudio — *intim*, nicht groß. Eignet sich für Nutzer mit musikalischer Sensibilität, in Konzentrationssituationen oder bei wertschätzendem Arbeitsstil. Die Identität bleibt erkennbar, solange das primäre Instrument ein gestrichenes oder angeschlagenes akustisches Klanginstrument ist und Hall klein bleibt; ein Synth-Pad würde sofort nach Horizont kippen, eine Solo-Gitarre mit Distortion nach Cityflow.

| | |
|---|---|
| **Sound-Typ** | Earcons (musikalische Motive) |
| **Klangmaterial** | Warmes Klavier (Hauptinstrument), Akustikgitarre (sekundär), gelegentlich gedämpftes Cello/Violine |
| **Tonalität** | Warmes Dur (C-Dur, F-Dur) für positive Gesten; relative Moll-Wendungen für Skip/Verfall |
| **Frequenzfokus** | Mittel (200–2000 Hz, Klavier-Sweetspot) |
| **Identitätsmerkmal** | Akustisch-instrumentaler Klangcharakter, intimer Raumeindruck (kleiner Hall) |
| **Charakter** | Warm, gediegen, präzise, persönlich, stilvoll |
| **Kernreferenz** | „Klavier in einem ruhigen Wohnzimmer, Spätnachmittag" |
| **Kernmotiv-Vorschlag** | Aufsteigende 3-Ton-Phrase C-E-G in der mittleren Oktave als Identitätsanker |

### Cityflow 🏙️

Eine hybride Welt zwischen Atmosphäre und Musik: Lo-Fi-Beats im 70–90 BPM-Bereich, warmer Rhodes oder gedämpftes Klavier, Vinyl-Crackle als ständiger Hintergrund-Marker, gelegentlich gedämpfte Snares oder weiche Hi-Hats. Vinyl-Crackle wirkt als *auditory icon* (Café-/Plattenspieler-Atmosphäre), die Beat-Hits wirken als *earcons* (rhythmische Akzente mit Bedeutung).

Genau diese Hybridität ist ihre Stärke und ihr Schwachpunkt: bei Tag-begonnen passt der Café-Morgen perfekt, beim Tagesabschluss greift das Vokabular zu kurz, weil ein Lo-Fi-Beat keine *Vollendungsgeste* hat — er kann nur abklingen oder gestoppt werden. Atmosphärisch verortet: Café, Heim-Office mit warmer Beleuchtung, Stadt von innen. Zielt auf Nutzer, die Konzentration mit subtilem urbanem Vibe kombinieren. Die Identität bleibt erkennbar, solange der Vinyl-Crackle-Layer hörbar ist und Beats lo-fi-typisch (nicht clean, nicht aggressiv) bleiben.

| | |
|---|---|
| **Sound-Typ** | Hybrid (Icon: Vinyl-Crackle / Earcon: Beat-Hits, Rhodes) |
| **Klangmaterial** | Rhodes E-Piano oder gedämpftes Klavier, weiche Snares/Hi-Hats, ständiger Vinyl-Crackle, Tape-Saturation |
| **Tonalität** | Warm, Dur-7-Akkorde (Cmaj7, Fmaj7), Lo-Fi-typische harmonische Sanftheit |
| **Tempo** | 70–85 BPM für rhythmische Sounds |
| **Frequenzfokus** | Mittel bis tief (warm, gerollte Höhen — keine harschen High-Frequencies) |
| **Identitätsmerkmal** | Vinyl-Crackle-Layer, warm-gerollte Klangfarbe |
| **Charakter** | Gemütlich, urban, jung, konzentriert mit Vibe |
| **Kernreferenz** | „Lo-Fi-Café an einem Regennachmittag" |
| **Kernmotiv-Vorschlag** | Weicher Rhodes-Akkord (Cmaj7) auf einem entspannten Lo-Fi-Beat-Anschlag |
| **Bekannter Schwachpunkt** | Tagesabschluss-Sounds — Lo-Fi-Vokabular hat keine eingebaute Vollendungsgeste; muss in der Methodik gezielt adressiert werden |

### Horizont 🌌

Eine rein elektronisch-synthetische Welt: weiche Synth-Pads, kristalline Bell-Synths, Reverb-getragene Klangflächen, gelegentlich subtile Modulationen (Filter-Sweeps, Phaser). Reines *earcon*-System wie Salon, aber in völlig anderem Klangidiom — synthetisch statt akustisch, weit statt intim, modal-offen statt tonal-geschlossen.

Atmosphärisch verortet zwischen Sci-Fi-Horizont, meditativem Pad-Album und modernem Ambient. Eignet sich für Nutzer, die zukunftsgewandte oder visionäre Stimmungen schätzen, oder für lange Konzentrationsphasen mit großem akustischem Raum. Die Identität bleibt erkennbar, solange Synth-Pads oder kristalline Klangflächen führen und akustische Instrumente abwesend sind; ein Klavier-Anschlag würde sofort nach Salon kippen.

| | |
|---|---|
| **Sound-Typ** | Earcons (synthetische Motive) |
| **Klangmaterial** | Synth-Pads (Wavetable, Additive), kristalline Bell-Synths, FM-Glocken, atmosphärische Drones |
| **Tonalität** | Offene Quinten, modal-suspended (Csus2, Fsus2), eher Dur-Charakter |
| **Frequenzfokus** | Breit gestreut, mit charakteristischer Brillanz im 5–10 kHz-Bereich (Pad-Schimmer) |
| **Identitätsmerkmal** | Reverb-getragene Räumlichkeit, synthetischer Klangcharakter |
| **Charakter** | Weit, ätherisch, zukunftsgewandt, ruhig, meditativ |
| **Kernreferenz** | „Sonnenaufgang über dem Ozean, Synth-Pad" |
| **Kernmotiv-Vorschlag** | Pad-Swell mit kristallinem Chime in F-Quinte als Identitätsanker |
| **Bekannter Schwachpunkt** | Tagesabschluss-Sounds — wie bei Cityflow fehlt der klare Resolutions-Punkt; Methodik muss explizite Closing-Gestik vorgeben |

---

## Klassifikation der aktiven Welten

Die fünf aktiven Welten decken bewusst unterschiedliche Sound-Design-Schulen ab, damit Nutzer mit unterschiedlichen klanglichen Vorlieben jeweils eine passende Welt finden:

| Welt | Schule | Achse |
|---|---|---|
| System | Earcons (technisch-harmonisch) | UI-Vokabular vs. musikalische Phrase |
| Morgenwald | Auditory icons (real) | Naturreferenz vs. Musik |
| Salon | Earcons (akustisch-traditionell) | Akustisch vs. synthetisch |
| Cityflow | Hybrid | Atmosphärisch vs. event-orientiert |
| Horizont | Earcons (synthetisch-ambient) | Akustisch vs. synthetisch |

Diese Klassifikation hat drei Konsequenzen für die Methodik:

- Auditory-Icon-Welten (Morgenwald) erfordern *konkrete Klangbeschreibungen* in Prompts, weil die Bedeutung über Naturreferenz entsteht
- Earcon-Welten mit musikalischer Phrase (Salon, Horizont, künftige Atelier/Bluesroom) erfordern *tonale und motivische Beschreibungen*, weil die Bedeutung über musikalische Geste entsteht
- Earcon-Welten ohne musikalische Phrase (System) erfordern *harmonische und klangcharakterliche Beschreibungen*, weil die Bedeutung ohne Bewegung erzeugt werden muss
- Die Hybrid-Welt (Cityflow) erfordert mehrere Prompt-Sprachen parallel — was sie anspruchsvoller macht

---

## Backlog-Welten

Welten, die konzeptionell durchdacht sind, aber nicht in Produktion stehen. Aufnahme in den aktiven Bestand setzt eine eigene Designentscheidung voraus — keine Welt entsteht, weil sie „nett wäre".

### Stille (priorisiert)

Eine designte Minimalwelt für sensible Nutzungssituationen, in denen die anderen Welten zu präsent wären — Mandantengespräch, Bibliothek, schlafendes Kind im Nebenzimmer, Vorstandssitzung. Auditory-Icon-Schule, sehr leise, sehr kurz: Holz-Tap, Papier-Knistern, leiser Atemzug.

Strategisch wichtig, weil sie eine Nutzungsbarriere abbaut, nicht nur einen Geschmack bedient — und damit anders als die anderen drei Backlog-Welten gewichtet ist. Steht in Konkurrenz und Komplementarität zur System-Welt: System ist *technisch-pragmatisch* minimal, Stille ist *designt* minimal.

| | |
|---|---|
| **Sound-Typ** | Auditory icons (minimal) |
| **Klangmaterial** | Holz-Tap, Papier-Knistern, leiser Atemzug, gedämpfte Tippsignale |
| **Identitätsmerkmal** | Ultra-leise, ultra-kurze Sounds; jede tonale Ausrichtung tabu |
| **Risiko** | Grenze zu „kein Sound" ist schmal — wenn die Welt zu gut ist, fragt der Nutzer, warum er nicht ganz ausschaltet |

### Atelier (Sax)

Eine ausdrucksstarke Earcon-Welt mit Saxophon als Lead-Instrument. Tenor- oder Altsax, gelegentliche Brushes-Drums, dezenter Walking-Bass. Earcons mit menschlicher Atemkomponente — der Sax-Anblas-Charakter macht die Welt persönlicher als Salon, abendlich-intim atmosphärisch verortet.

Designherausforderung: Bei Mikro-Sounds (Teilaufgabe erledigt = 0.5 s) muss der Sax-Charakter trotzdem hörbar bleiben, sonst klingt es wie eine generische Warm-Note.

### Bluesroom (Blues/Jazz)

Eine Earcon-Welt mit dem Idiom von Blues und Jazz als prägendem Charakter, ohne sich auf ein einzelnes Instrument festzulegen — gedämpfte Trompete, Hammond-Orgel, Kontrabass-Pizzicato, Brushes. Ensemble-Welt mit blue notes, dom7-Akkorden, swing-Phrasierung.

Designherausforderung: Jazz/Blues lebt von Improvisation und Spannungsaufbau, unsere Sounds sind 0.5–2.5 Sekunden. Lösung wird sein, mit *Geste* zu arbeiten statt mit *Phrase* — einzelner Trompeten-Stoß, einzelner Walking-Bass-Schritt.

### Pavillon (asiatisch-meditativ)

Eine Earcon-Welt mit asiatischer Tonsprache — Koto, Shakuhachi, kleine Bronzeglocken, Bambusbrunnen. Pentatonische Tonleitern, viel Raum zwischen Tönen, andere Vorstellung von „Resolution" als westliche Tonalität.

Designherausforderung zweifach: kulturelle Aneignungsfrage (eigene Beurteilung erforderlich) und musikalisch-handwerkliche Anforderung — pentatonische Phrasen klingen schnell klischeehaft, wenn sie nicht sehr sorgfältig gestaltet sind. ElevenLabs ist auf westlichem Material trainiert; Authentizität nicht garantiert.

---

## Welten, die ausdrücklich nicht aufgenommen werden

Diese Linie schützt das Konzept vor Beliebigkeit:

- **Vokale Welt** (Chor, Atelier mit Stimmen) — Stimmen ohne Worte irritieren; Nutzer hören unbewusst nach Sprache und werden abgelenkt. Funktioniert in Game-Audio, ist für eine Tagesplaner-App zu invasiv.
- **Verspielt** (Spielhaus, Glockenspiel, Spieldose) — Charme-Risiko zu hoch. Wirkt in einer professionellen App schnell infantil.
- **Cinematic / Orchestral** — zu groß. Sound-Weight passt nicht zur Mikrointeraktions-Skala; „Teilaufgabe erledigt" als orchestrale Geste wäre lächerlich.

Die Liste ist nicht abschließend, aber sie zeigt das Bewertungsmuster: Eine Welt muss zu MyCadenza passen — nicht zu jedem denkbaren Klangidiom.

---

## Verweise

- [`MCK_Manifest.md`](MCK_Manifest.md) — Designprinzipien, von denen die Charta-Entscheidungen abgeleitet sind
- [`MCK_Methodik.md`](MCK_Methodik.md) — wie Charta-Setzungen in konkrete Prompts übersetzt werden
- [`MCK_Prompts.md`](MCK_Prompts.md) — die konkreten ElevenLabs-Prompts pro Aktion × Welt
- [`MCK_Bewertung.xlsx`](MCK_Bewertung.xlsx) — Bewertungsmatrix der bisherigen WAVs (Grundlage für Sanierungsbedarf)
