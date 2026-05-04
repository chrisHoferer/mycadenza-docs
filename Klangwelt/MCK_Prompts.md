# MyCadenza – ElevenLabs-Prompts pro Ereignis

> Sortiert nach Ereignis (Funktions-Merkmal als gemeinsames Hör-Merkmal über alle Klangwelten).
> Pro Ereignis je Klangwelt zwei Prompt-Vorschläge zur Auswahl.
> Stand: 04.05.2026

---

## Verwendung mit ElevenLabs Sound Effects

- Plan: Starter (Commercial License, WAV 48 kHz)
- Tool generiert ca. 10–20 % kürzer als spezifiziert → Längen-Angaben großzügig wählen
- Ziel-Längen: Tag-Aktionen 1.0–1.5 Sek · Klicks (Teilaufgabe) 0.3–0.5 Sek · Hauptaufgabe komplett 1.2–1.5 Sek
- Workflow: Prompt eingeben → 4 Varianten generieren → beste auswählen → in Bewertungstabelle dokumentieren

## Klangwelten-Charakter

| Klangwelt | Charakter | Akustische Sprache |
|---|---|---|
| **System** | iOS-Standard, neutral, klar | Soft pings, gentle clicks, simple synth tones, UI-typisch |
| **Morgenwald** | Natur, organisch, hell, lebendig | Vogelstimmen, Wassertropfen, Holz, Blätter, leichter Wind |
| **Salon** | Klavier-zentriert, klassisch, warm | Solo-Klaviertöne, kurze Akkorde, kammermusikalische Andeutungen |
| **Cityflow** | Urban, modern, smooth, leicht synthetisch | Elektroklavier, weiche Synths, Jazz-Anklänge, Café-Atmosphäre |
| **Horizont** | Ätherisch, weit, träumerisch, magisch | Ambient-Pads, Glasklänge, Sparkles, Hall-Räume |

---

## Tagbezug (Stamm-Aktionen ohne Fallback)

### `tagBegonnen` — Tag begonnen

**Semantik:** Frischer Start, hell, einladend. Optimistischer Anfang. **Länge:** 1.0–1.5 Sek.

**System**
- Soft single bell tone, neutral UI start sound, warm and clear, 1.2 seconds
- Gentle two-note ascending synth ping, iOS-style welcoming, 1.0 second

**Morgenwald**
- Single bird chirp at dawn, bright and welcoming, with subtle wood reverb, 1.2 seconds
- Soft wooden flute note followed by a distant bird call, fresh forest morning, 1.4 seconds

**Salon**
- Single C5 piano note, soft attack, warm sustain, with subtle hall reverb, 1.2 seconds
- Two-note piano arpeggio C–E, gentle and inviting, classical concert hall ambience, 1.0 second

**Cityflow**
- Soft electric piano chord, smooth jazz inflection, modern café morning, 1.2 seconds
- Mellow synth pad with subtle vibrato, urban awakening, 1.4 seconds

**Horizont**
- Ethereal sparkle with ascending shimmer, dreamlike awakening, ambient reverb, 1.4 seconds
- Soft glass chime with gentle harmonic overtones, magical morning, 1.2 seconds

---

### `tagKomplett` — Tag komplett

**Semantik:** Belohnung, kleines Triumphgefühl, Tag durchgezogen. **Länge:** 1.2–1.5 Sek.

**System**
- Triumphant three-note ascending synth fanfare, bright and rewarding, 1.4 seconds
- Crisp double-chime with confirming low-note ending, achievement notification, 1.3 seconds

**Morgenwald**
- Cluster of birds in joyful chorus, bright and uplifting, forest celebration, 1.4 seconds
- Wooden xylophone three-note rising melody followed by a single bird trill, 1.5 seconds

**Salon**
- Three-note ascending piano flourish ending on a major chord, warm and triumphant, 1.4 seconds
- Brief piano arpeggio C–E–G with held final note, classical resolution, hall reverb, 1.5 seconds

**Cityflow**
- Smooth jazz piano flourish with light cymbal sparkle, urban accomplishment, 1.4 seconds
- Warm synth pad swelling into a confirmation chord, modern reward, 1.3 seconds

**Horizont**
- Ascending sparkle cascade ending with a held ethereal pad, magical fulfillment, 1.5 seconds
- Crystalline three-note chime with cosmic shimmer tail, dreamlike completion, 1.4 seconds

---

### `tagBeendet` — Tag beendet

**Semantik:** Abschluss, friedlich, Ruhe einkehrend. **Länge:** 1.0–1.5 Sek.

**System**
- Single soft descending synth tone, calm closing notification, 1.2 seconds
- Gentle two-note descending bell, peaceful end-of-session, 1.3 seconds

**Morgenwald**
- Single owl hoot at dusk with distant wind, peaceful forest evening, 1.4 seconds
- Soft wooden chime with descending leaves rustling, calming forest closure, 1.5 seconds

**Salon**
- Descending piano two-note phrase G–C in low register, peaceful resolution, hall reverb, 1.4 seconds
- Soft minor-major piano cadence with held final note, gentle classical closing, 1.5 seconds

**Cityflow**
- Mellow electric piano descending phrase, late-evening jazz café, 1.4 seconds
- Soft synth pad fading with descending bass note, calm urban dusk, 1.5 seconds

**Horizont**
- Descending ethereal pad with soft glass tail, twilight closure, 1.5 seconds
- Single deep bell with shimmering overtones fading slowly, cosmic end, 1.4 seconds

---

## Aufgabenbezug

### `aufgabeFaellig` — Aufgabe fällig

**Semantik:** Aufmerksamkeit, höflicher Hinweis. **Fallback:** `tagBegonnen`. **Länge:** 0.7–1.0 Sek.

**System**
- Soft single notification ping, neutral attention tone, 0.8 seconds
- Two-note synth alert, polite and clear, iOS-style reminder, 0.9 seconds

**Morgenwald**
- Single woodpecker tap echoing softly, gentle attention call, 0.8 seconds
- Brief bird chirp with wooden block resonance, forest reminder, 0.9 seconds

**Salon**
- Single E5 piano note with soft attack, polite reminder, 0.8 seconds
- Brief two-note piano motif G–B, classical attention cue, 0.9 seconds

**Cityflow**
- Soft electric piano single note with subtle delay, modern reminder, 0.8 seconds
- Brief synth pluck with warm decay, urban attention tone, 0.9 seconds

**Horizont**
- Single soft glass chime with sparkle tail, ethereal reminder, 0.9 seconds
- Brief shimmer with ascending micro-sparkle, magical attention, 0.8 seconds

---

### `aufgabeUeberfaellig` — Aufgabe überfällig

**Semantik:** Warnzeichen, eindringlicher als „fällig", aber nicht endgültig. **Fallback:** `aufgabeFaellig`. **Länge:** 0.8–1.0 Sek.

**System**
- Repeated double-tap notification, slightly more urgent, 0.9 seconds
- Two ascending notes with sharper attack, attention-getting iOS warning, 1.0 second

**Morgenwald**
- Double woodpecker knock with slight echo, urgent forest tap, 0.9 seconds
- Two quick bird alarm calls in succession, alert nature signal, 1.0 second

**Salon**
- Two repeated piano notes with growing intensity, classical urgency, 1.0 second
- Sharp staccato piano motif, three quick taps, attention-demanding, 0.9 seconds

**Cityflow**
- Two synth taps with slight pitch rise, modern urgency cue, 0.9 seconds
- Brief electric piano double-hit with pulsing warmth, urban warning, 1.0 second

**Horizont**
- Two glass chimes with rising shimmer, ethereal warning, 1.0 second
- Pulsing sparkle with insistent attack, magical urgency, 0.9 seconds

---

### `aufgabeVerfallen` — Aufgabe verfallen

**Semantik:** Endgültig versäumt, Abschluss ohne Erfolg. **Fallback:** `teilaufgabeUebersprungen`. **Länge:** 1.0–1.3 Sek.

**System**
- Single low descending tone, neutral end-of-validity, 1.1 seconds
- Soft minor-key two-note descent, polite expiry notification, 1.2 seconds

**Morgenwald**
- Single dry leaf rustle ending with soft thud, fallen forest closure, 1.2 seconds
- Distant wood creak with gentle wind tail, expired natural moment, 1.3 seconds

**Salon**
- Descending minor piano interval E–C with hall fade, classical expiry, 1.2 seconds
- Single low piano note with slow decay, gentle finality, 1.1 seconds

**Cityflow**
- Soft synth descending phrase with low bass note, urban expiry, 1.2 seconds
- Mellow electric piano minor chord fading slowly, modern closure, 1.3 seconds

**Horizont**
- Slowly descending ethereal sparkle with reverb tail, dreamlike expiry, 1.3 seconds
- Single dark bell with shimmering decay, cosmic finality, 1.2 seconds

---

## Settings

### `exportImport` — Export / Import

**Semantik:** Erfolg, Daten-Transfer abgeschlossen. **Fallback:** `hauptaufgabeKomplett`. **Länge:** 0.8–1.0 Sek.

**System**
- Quick three-note ascending data-success ping, iOS-style transfer confirmation, 0.9 seconds
- Crisp double-beep with rising tone, neutral success notification, 0.8 seconds

**Morgenwald**
- Quick wooden xylophone run upward, fresh forest confirmation, 0.9 seconds
- Two bird notes followed by a soft wooden tap, natural success, 1.0 second

**Salon**
- Three-note piano arpeggio rising C–E–G, polite classical confirmation, 0.9 seconds
- Brief piano flourish ending on major chord, hall ambience, 1.0 second

**Cityflow**
- Smooth synth ascending three-note phrase, modern data success, 0.9 seconds
- Electric piano quick flourish with warm tail, urban confirmation, 1.0 second

**Horizont**
- Sparkle cascade ascending into bright shimmer, ethereal success, 1.0 second
- Quick crystalline rise with cosmic glow, magical confirmation, 0.9 seconds

---

## Administration

### `aufgabeErstellt` — Aufgabe erstellt

**Semantik:** Etwas Neues, leicht, freundlich. **Fallback:** `teilaufgabeErledigt`. **Länge:** 0.4–0.6 Sek.

**System**
- Soft pop with brief synth tail, gentle add notification, 0.5 seconds
- Quick two-note rising ping, iOS-style item-added cue, 0.5 seconds

**Morgenwald**
- Single soft wooden tap with brief leaf rustle, fresh forest add, 0.5 seconds
- Brief bird chirp with bright attack, nature appearance, 0.4 seconds

**Salon**
- Single high piano note with quick decay, classical add cue, 0.5 seconds
- Brief two-note piano touch C–E, gentle appearance, 0.4 seconds

**Cityflow**
- Soft synth pluck with warm tail, modern add notification, 0.5 seconds
- Brief electric piano note with subtle reverb, urban appearance, 0.4 seconds

**Horizont**
- Single sparkle with brief shimmer tail, ethereal add, 0.5 seconds
- Soft glass chime with quick decay, magical appearance, 0.4 seconds

---

### `aufgabeGeloescht` — Aufgabe gelöscht

**Semantik:** Sanfte Entfernung, neutral. **Fallback:** `teilaufgabeUebersprungen`. **Länge:** 0.4–0.6 Sek.

**System**
- Soft brief whoosh with descending tone, neutral delete cue, 0.5 seconds
- Quick low-pitched click with subtle decay, iOS-style removal, 0.4 seconds

**Morgenwald**
- Soft wood creak with descending leaf rustle, gentle nature removal, 0.5 seconds
- Brief twig snap with airy tail, forest delete, 0.4 seconds

**Salon**
- Descending two-note piano touch with quick decay, classical removal, 0.5 seconds
- Soft single low piano note, gentle delete, 0.4 seconds

**Cityflow**
- Soft synth descending pluck, modern removal cue, 0.5 seconds
- Brief electric piano low note with airy tail, urban delete, 0.4 seconds

**Horizont**
- Soft descending sparkle dissipating, ethereal removal, 0.5 seconds
- Brief glass chime fading downward, magical delete, 0.4 seconds

---

### `erledigteEntfernt` — Erledigte entfernt

**Semantik:** Aufräumen, Sweep, leichter Schwung. **Fallback:** `teilaufgabeUebersprungen`. **Länge:** 0.5–0.8 Sek.

**System**
- Brief sweeping whoosh with light synth wash, neutral cleanup, 0.6 seconds
- Quick descending arpeggio with airy tail, iOS-style sweep, 0.7 seconds

**Morgenwald**
- Soft wind sweep through dry leaves, natural cleanup, 0.7 seconds
- Brief brush of pine needles with airy tail, forest tidy, 0.6 seconds

**Salon**
- Quick descending piano glissando, classical cleanup, 0.7 seconds
- Soft brushed piano string with airy decay, gentle sweep, 0.6 seconds

**Cityflow**
- Soft synth sweep with descending pad, modern cleanup, 0.7 seconds
- Brief electric piano glissando with warm tail, urban tidy, 0.6 seconds

**Horizont**
- Sparkle dispersal with descending shimmer, ethereal cleanup, 0.7 seconds
- Soft glass cascade fading downward, magical sweep, 0.6 seconds

---

### `aufgabeZurueckgesetzt` — Aufgabe zurückgesetzt

**Semantik:** Reset, Neustart, vorsichtige Wiederbereitschaft. **Fallback:** `teilaufgabeUebersprungen`. **Länge:** 0.5–0.7 Sek.

**System**
- Soft two-note phrase with returning pitch, neutral reset cue, 0.6 seconds
- Quick rewind-style synth blip, iOS-style restart, 0.5 seconds

**Morgenwald**
- Single wooden block tap followed by gentle bird whistle, forest reset, 0.6 seconds
- Soft water droplet ripple with brief leaf rustle, natural restart, 0.7 seconds

**Salon**
- Two-note piano phrase E–C reversing in pitch, classical reset, 0.6 seconds
- Soft single piano note with gentle echo return, restart, 0.7 seconds

**Cityflow**
- Soft synth pluck with reversed tail, modern reset cue, 0.6 seconds
- Brief electric piano returning phrase, urban restart, 0.7 seconds

**Horizont**
- Soft sparkle with reversed shimmer, ethereal reset, 0.7 seconds
- Brief glass chime with returning echo, magical restart, 0.6 seconds

---

## Prozess (drei Stamm-Aktionen ohne Fallback, drei mit Fallback)

### `teilaufgabeErledigt` — Teilaufgabe erledigt

**Semantik:** Kleines Akkomplisment, kurzer befriedigender Click. **Stamm-Aktion.** **Länge:** 0.3–0.5 Sek.

**System**
- Single crisp tick with subtle synth tail, iOS-style task tick, 0.4 seconds
- Soft pop with minimal decay, neutral check cue, 0.3 seconds

**Morgenwald**
- Single bright wooden tick with leaf brush, forest checkbox, 0.4 seconds
- Soft pebble drop into water, brief and clean, 0.3 seconds

**Salon**
- Single staccato piano note in upper register, crisp classical tick, 0.4 seconds
- Brief piano key tap with quick decay, gentle check, 0.3 seconds

**Cityflow**
- Single soft synth tick with warm bottom, modern checkbox, 0.4 seconds
- Brief electric piano tap with airy tail, urban tick, 0.3 seconds

**Horizont**
- Single tiny sparkle with brief shimmer, ethereal tick, 0.4 seconds
- Soft glass tap with quick fade, magical check, 0.3 seconds

---

### `teilaufgabeUebersprungen` — Teilaufgabe übersprungen

**Semantik:** Bewusst übergangen, neutral, kein Tadel. **Stamm-Aktion.** **Länge:** 0.3–0.5 Sek.

**System**
- Single brief mid-pitch click, neutral skip cue, 0.4 seconds
- Soft swipe-style synth blip, iOS-style pass, 0.3 seconds

**Morgenwald**
- Soft leaf rustle with brief pass, gentle forest skip, 0.4 seconds
- Brief wind whisk through grass, natural pass, 0.3 seconds

**Salon**
- Single muted piano note with quick decay, classical skip, 0.4 seconds
- Brief piano string brush, gentle pass, 0.3 seconds

**Cityflow**
- Soft synth swipe with warm tail, modern skip, 0.4 seconds
- Brief electric piano muted note, urban pass, 0.3 seconds

**Horizont**
- Brief sparkle dissolving sideways, ethereal skip, 0.4 seconds
- Soft glass slide with quick fade, magical pass, 0.3 seconds

---

### `hauptaufgabeErledigt` — Hauptaufgabe erledigt

**Semantik:** Größere Errungenschaft als Teilaufgabe, einzelne Hauptaufgabe abgeschlossen (ohne Teilaufgaben). **Fallback:** `hauptaufgabeKomplett`. **Länge:** 0.7–1.0 Sek.

**System**
- Three-note ascending confirmation chime, satisfying completion, 0.9 seconds
- Crisp two-tone bell with rising pitch, iOS-style achievement, 0.8 seconds

**Morgenwald**
- Two bright bird notes followed by a wooden chime, forest accomplishment, 0.9 seconds
- Single bird trill with wooden xylophone tail, natural success, 1.0 second

**Salon**
- Three-note piano arpeggio C–E–G, satisfying classical completion, 0.9 seconds
- Brief piano flourish with held final note, hall ambience, 1.0 second

**Cityflow**
- Smooth electric piano three-note rise, modern achievement, 0.9 seconds
- Soft synth pad with confirmation chord, urban success, 1.0 second

**Horizont**
- Three-note sparkle ascent with shimmer tail, ethereal accomplishment, 1.0 second
- Bright glass chime cascade upward, magical success, 0.9 seconds

---

### `hauptaufgabeUebersprungen` — Hauptaufgabe übersprungen

**Semantik:** Bewusster Skip einer ganzen Hauptaufgabe, deutlicher als Teilaufgabe-Skip. **Fallback:** `teilaufgabeUebersprungen`. **Länge:** 0.5–0.8 Sek.

**System**
- Two-note descending pass with subtle low end, neutral skip cue, 0.7 seconds
- Soft swipe with low confirming tone, iOS-style deliberate pass, 0.6 seconds

**Morgenwald**
- Soft wind sweep ending with low wooden thud, deliberate forest skip, 0.7 seconds
- Two leaf rustles followed by gentle pause, natural pass, 0.6 seconds

**Salon**
- Two-note descending piano phrase with hall reverb, classical skip, 0.7 seconds
- Brief muted piano chord fading downward, gentle pass, 0.6 seconds

**Cityflow**
- Soft synth descending phrase with warm bass, modern skip, 0.7 seconds
- Electric piano two-note pass with airy tail, urban deliberate skip, 0.6 seconds

**Horizont**
- Descending sparkle with deeper shimmer tail, ethereal skip, 0.7 seconds
- Soft glass chime sliding downward, magical pass, 0.6 seconds

---

### `hauptaufgabeKomplett` — Hauptaufgabe komplett

**Semantik:** Alle Teilaufgaben erledigt, größter Belohnungs-Sound im täglichen Workflow. **Stamm-Aktion.** **Länge:** 1.2–1.5 Sek.

**System**
- Four-note ascending fanfare with synth pad swell, iOS-style major achievement, 1.4 seconds
- Triumphant double-chime with held bright tail, rewarding completion, 1.5 seconds

**Morgenwald**
- Joyful bird chorus with wooden xylophone flourish, forest celebration, 1.5 seconds
- Cascade of bright bird notes ending with held wooden chime, natural triumph, 1.4 seconds

**Salon**
- Piano flourish C–E–G–C ending on held major chord, classical triumph with hall reverb, 1.5 seconds
- Bright four-note piano cascade with concert-hall sparkle, joyful classical resolution, 1.4 seconds

**Cityflow**
- Smooth jazz piano flourish ending with held synth pad, modern triumph, 1.5 seconds
- Bright electric piano cascade with cymbal sparkle, urban accomplishment, 1.4 seconds

**Horizont**
- Brilliant sparkle cascade with held shimmering pad, magical fulfillment, 1.5 seconds
- Crystalline four-note ascent ending with cosmic glow, dreamlike triumph, 1.4 seconds

---

### `ueberfaelligErledigt` — Überfällig erledigt

**Semantik:** Erleichterung, spät dran aber geschafft. Nicht reine Belohnung, sondern Aufatmen. **Fallback:** `hauptaufgabeKomplett`. **Länge:** 1.0–1.3 Sek.

**System**
- Two-note rising tone with relieving sustain, iOS-style late-success, 1.2 seconds
- Soft confirmation chime with longer warm tail, relieved completion, 1.1 seconds

**Morgenwald**
- Single bird call followed by long held wooden chime, relieved forest moment, 1.2 seconds
- Soft wind whoosh ending with bright bird trill, natural late accomplishment, 1.3 seconds

**Salon**
- Piano two-note rise C–G with held sustain, classical relief, 1.2 seconds
- Soft piano chord with extended hall reverb, gentle late triumph, 1.3 seconds

**Cityflow**
- Mellow electric piano rising phrase with held warmth, urban relief, 1.2 seconds
- Soft synth pad swell with confirming chord, modern late-success, 1.3 seconds

**Horizont**
- Slow sparkle ascent with held shimmering tail, ethereal relief, 1.3 seconds
- Soft glass chime rising and held with cosmic glow, magical late accomplishment, 1.2 seconds

---

## Empfohlener Bewertungs-Workflow

1. Pro Ereignis × Klangwelt einen der Prompts in ElevenLabs eingeben.
2. Vier Varianten generieren lassen.
3. Beste Variante auswählen, optional kürzen/anpassen.
4. WAV laden, in App einbauen, in der Bewertungstabelle (`MyCadenza_Klangwelt_Bewertung.xlsx`) eintragen.
5. Falls keine der vier Varianten passt: alternativen Prompt aus der Liste probieren oder eigenen Prompt schreiben.

## Verweise

- Schema-Doku: `MyCadenza_Klangwelt_Dateischema.md`
- Bewertungstabelle: `MyCadenza_Klangwelt_Bewertung.xlsx`
- Code-Definition: `SoundManager.swift` (App-Repo)
- Bestehende Prompts (vor 04.05.2026): `Cadenza_ElevenLabs_Prompts.md`
