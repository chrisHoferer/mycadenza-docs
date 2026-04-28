# MyCadenza — Feature-Konzepte v1.7.x

Konzept-Dokument für zwei eng verzahnte Erst-Anwender-Features:
**Default-Kategorien** und **Mustertemplate „MyCadenza einrichten"**.

Entwurfsstand: 28.04.2026

---

## 1. Kontext und Motivation

Der erste Eindruck von MyCadenza nach Installation und Onboarding ist heute
eine leere App. Das ist konsistent zum „dein Tag, deine Melodie"-Versprechen
— aber es lädt keine Welt ein, sondern überlässt dem Nutzer die Aufgabe,
sich Struktur erst selbst zu bauen.

Für eine App, deren Designsprache Klangbild, Farbpalette und Klangwelten zu
einer kuratierten Identität verbindet, ist die leere App ein verschenkter
Moment. Der Nutzer hört nichts, sieht keine Farbe in Aktion, hat kein Gefühl
für die kuratierte Ästhetik — bis er aktiv etwas anlegt.

Zwei Features adressieren das gemeinsam:

- **Default-Kategorien** geben *Struktur*: vier Kategorien werden beim
  Erst-Start angelegt, jede mit einer eigenen Klangwelt, Farbe und Icon. Der
  Nutzer erlebt die Designsprache durch Sehen und (sobald er etwas erledigt)
  Hören.
- **Mustertemplate „MyCadenza einrichten"** gibt *Aktion*: eine konkrete erste
  Aufgabe steht direkt nach dem Onboarding in der Heute-Ansicht und führt
  durch die Kernfunktionen der App.

Beide Features teilen die gleiche technische Grundlage: eine sync-aware
Erst-Start-Erkennung, die sich nicht von verzögertem CloudKit-Sync überraschen
lässt. Diese Logik wird mit B-M2 (Build 10721, Import-Sperre) entwickelt und
in v1.7.x für die zwei Features wiederverwendet.

---

## 2. Feature 1 — Default-Kategorien für Neuanwender

### 2.1 Konzept und Begründung

Beim allerersten Start auf einer leeren Datenbasis legt MyCadenza vier
Kategorien an. Diese Kategorien:

- decken den Alltag der meisten Nutzer in groben Lebensbereichen ab
- sind generisch genug, um nicht aufdringlich zu wirken
- konkret genug, um nicht beliebig zu sein
- jede Kategorie aktiviert eine eigene Klangwelt, sodass der Nutzer beim
  ersten Tagesgeschäft die unterschiedlichen Welten erlebt

### 2.2 Die vier Kategorien

| Kategorie | Klangwelt | Farbe (vorläufig) | Icon (vorläufig) |
|---|---|---|---|
| Haushalt | Morgenwald | Grün | (offen) |
| Arbeit | Cityflow | Blau | (offen) |
| Family & Friends | Salon | Pink / Korall | (offen) |
| Gesundheit | Horizont | Petrol | (offen) |

**Klangwelt-Zuordnung — Begründung:**

- *Haushalt → Morgenwald* — erdig, ruhig, alltäglich. Passt zur häuslichen
  Routine. Hat den schönen Nebeneffekt, dass die *erste Aufgabe*, die ein
  neuer Nutzer beim Mustertemplate erledigt, einen besonders einladenden
  Sound bekommt.
- *Arbeit → Cityflow* — urban, fokussiert, leiser Antrieb. Stimmungsbild
  einer produktiven Bürowelt.
- *Family & Friends → Salon* — warm, gesellig, menschlich. Trägt das
  Soziale klanglich mit.
- *Gesundheit → Horizont* — weit, atmend, regenerativ. Verweist auf das
  Längerfristige, Selbstfürsorge.

**Farbe und Icon** sind als vorläufig markiert. Sie werden in einer eigenen
Designsession finalisiert, idealerweise zusammen mit einem Test der vier
Kategorien in der App, sodass die Stimmigkeit von Farbe + Icon + Klangwelt
geprüft werden kann.

**Lokalisierung der Kategorienamen** erfolgt beim Anlegen nach
Bundle-Sprache. DE-Bundle legt deutsche Namen an, EN-Bundle englische.
Sprachwechsel danach ändert die Namen *nicht* — das ist konsistent mit dem
aktuellen Localization-Modell und schützt nutzerangelegte Übersetzungen oder
Anpassungen vor späterer Verwirrung.

### 2.3 Trigger-Logik (Sync-aware Erst-Start-Erkennung)

Das größte Risiko ist eine Race-Condition mit CloudKit-Sync. Wenn die
Seed-Logik beim Erst-Start läuft, *bevor* CloudKit-Daten ankommen,
kollidieren beide Quellen — der Nutzer sieht plötzlich seine echten
Kategorien aus iCloud *und* die Defaults nebeneinander.

Besonders kritisch: Installation auf einem zweiten Gerät. Lokale DB ist leer,
Defaults werden angelegt, gleichzeitig beginnt CloudKit-Download der echten
Daten — Duplikate sind die Folge.

**Lösungsansatz: zweistufige Prüfung.**

```
Onboarding läuft → CloudKit-Initial-Sync starten → warten bis Sync-Status klar →
Wenn DB nach Sync leer ist → Defaults anlegen
Wenn DB nach Sync Daten enthält → keine Defaults, vorhandene Daten bleiben
```

Diese Logik wird mit B-M2 (Build 10721) entwickelt und für das Default-Seeding
wiederverwendet. Implementierung als eigene Etappe nach 10721, Build-Nummer
folgt der dann gültigen Reihenfolge.

### 2.4 Onboarding-Anpassung (Screen 4 adaptiv)

Onboarding-Screens 1 bis 3 bleiben wie heute. Während dieser Screens läuft
der Sync-Check parallel im Hintergrund.

**Screen 4 wird adaptiv** — drei Varianten je nach Sync-Status:

- **Variante A — keine Daten in iCloud, Sync abgeschlossen:** Begrüßung
  „Wir richten dir ein paar Beispiele ein, mit denen du sofort loslegen
  kannst" — Defaults werden angelegt, Mustertemplate wird angelegt,
  Sample-Aufgabe wird generiert.
- **Variante B — Daten in iCloud gefunden:** Begrüßung „Wir haben deine
  Daten aus iCloud geladen" — keine Defaults, kein Sample, der Nutzer
  landet direkt in seiner gewohnten Welt.
- **Variante C — Sync dauert noch:** Begrüßung „Während wir mit iCloud
  abstimmen, richten wir dir Beispiele ein. Falls iCloud Daten hat, werden
  diese später geladen und haben Vorrang" — Defaults werden angelegt mit
  expliziter Markierung, sodass ein nachträglicher Sync sie ggf. ersetzen
  kann (siehe 3.3 zum Marker `isSampleData`).

Variante C ist die heikelste — sie braucht einen sauberen Konflikt-Mechanismus,
falls iCloud-Daten verspätet ankommen.

### 2.5 Bestandsschutz

Nutzer von v1.6.1 oder früher dürfen weder Default-Kategorien dazubekommen
noch nach ihrem Wunsch gefragt werden. Sie haben bereits ihre Welt
aufgebaut.

Erkennung „Erstinstallation" über AppStorage-Flag plus zusätzlich Sync-Status
(siehe 2.3). Beide Bedingungen müssen für eine Default-Anlage erfüllt sein.

### 2.6 Offene Punkte

- Finale Farben (vorläufige Tönung muss verifiziert werden, besonders im
  Kontrast zur bestehenden 45-Farben-Palette)
- Icon-Auswahl je Kategorie aus dem bestehenden Icon-Set
- Englische Übersetzungen der Kategorienamen
  (Haushalt → Household? Home? — Wahl mit Bedacht treffen)
- UX-Detail: Erlauben wir dem Nutzer, einzelne Defaults im Onboarding
  abzuwählen? Oder werden alle vier angelegt und der Nutzer löscht
  unerwünschte später selbst? — Tendenz: alle vier anlegen, weil
  Abwahl-UI das Onboarding verkompliziert.

---

## 3. Feature 2 — Mustertemplate „MyCadenza einrichten"

### 3.1 Konzept

Beim Erst-Start entstehen zwei verbundene Datenobjekte:

1. **Ein Template** „MyCadenza einrichten" mit `isSampleData = true` —
   bleibt als kuratierte Vorlage in der Template-Liste. Damit kann der
   Nutzer es später erneut ausführen, betrachten oder anpassen. Es ist auch
   die Quelle für künftige Versions-Updates des Lern-Inhalts.
2. **Eine konkrete Aufgabe** — sofort beim Onboarding-Ende aus diesem
   Template generiert. Diese Aufgabe ist *keine* Sample-Aufgabe mehr,
   sondern eine ganz normale User-Aufgabe ab dem Moment ihrer Erzeugung
   (siehe 3.3).

Diese Trennung hat einen wichtigen Vorteil: Die Anwendung „aus Template
Aufgabe machen" ist eine bestehende, getestete Funktion der App. Der
Onboarding-Flow nutzt diesen Standard-Pfad, statt eine Sonderlogik zu
implementieren. Der Nutzer sieht nichts Magisches, sondern das normale
Verhalten der App — und kennt das Muster bereits, wenn er später selbst
„aus Template Aufgabe" anwendet.

### 3.2 Inhaltsstruktur des Templates

Hauptaufgabe: **„MyCadenza einrichten"**

Teilaufgaben (Lern-Container, Reihenfolge bewusst gewählt):

1. *Eine eigene Kategorie anlegen oder anpassen* — der Nutzer macht die
   Defaults zu seinen.
2. *Klangwelten der Kategorien einmal durchhören* — Verständnis für die
   akustische Identität.
3. *Eine erste eigene Aufgabe erstellen* — der zentrale Workflow.
4. *Eine Aufgabe erledigen — auf den Sound achten* — die Klangwelt-Belohnung
   bewusst erleben.
5. *Eine wiederkehrende Aufgabe einrichten* — fortgeschrittenes Feature
   einführen.
6. *Tag-Beginn-Sound einstellen* — die rituellen Akzente der App entdecken.

Jede Teilaufgabe hat einen kurzen, freundlich formulierten Beschreibungstext.

**Kategoriezuordnung:** Haushalt → Klangwelt Morgenwald. Damit erlebt der
Nutzer beim Erledigen der ersten Teilaufgabe den ruhigsten und einladendsten
der Klänge.

**Verfall:** `doesNotExpire = true`. Das Template ist kein Tagesgeschäft,
sondern ein Lern-Container in eigenem Tempo.

### 3.3 Marker `isSampleData` — nur am Template, nicht an der Aufgabe

Der Marker dient ausschließlich der Update-Logik:

- Wenn ein neues App-Release das Template inhaltlich verbessert, kann die App
  beim Update prüfen: Ist das vorhandene Template noch
  `isSampleData = true` *und* unverändert vom Nutzer? → Dann durch neue
  Version ersetzen.
- Hat der Nutzer das Template angepasst (Beschreibung geändert, Teilaufgaben
  hinzugefügt, andere Kategorie zugewiesen)? → `isSampleData` wird auf
  `false` gesetzt oder das Original-Hash-Feld stimmt nicht mehr überein →
  *nicht* überschreiben. Der Nutzer hat es zu seinem gemacht.

Die **generierte Aufgabe** trägt den Marker bewusst nicht. Sie ist ab dem
Moment ihrer Erzeugung eine normale User-Aufgabe, die genauso gelöscht,
verschoben, umbenannt oder erledigt wird wie jede andere. Damit:

- bleibt das Update-Mechanismus auf das Template beschränkt
- gibt es keine versteckte Sonderbehandlung zur Laufzeit
- folgt die App auch hier dem Prinzip „normaler Pfad, keine Magie"

### 3.4 Sample-Aufgabe-Generation beim Onboarding

Am Ende des Onboardings (nach Screen 4, Variante A oder C):

```
Template wird angelegt → aus Template Aufgabe generiert →
Startzeit = jetzt (genauer Zeitpunkt des Onboarding-Endes) →
doesNotExpire = true (übernommen vom Template) →
Aufgabe landet direkt in der Heute-Ansicht
```

**Bewusste Designentscheidungen:**

- **Startzeit „jetzt", nicht „nächste halbe Stunde"** — die Aufgabe soll
  präsent in Heute stehen, nicht im Anstehend-Block versteckt sein.
- **Kein Verfall** — wenn der Nutzer die App mehrere Tage ignoriert, bleibt
  die Lern-Aufgabe stehen. Keine frustrierende verschwundene erste Aufgabe.
- **Authentische Startzeit** — auch wenn der Nutzer das Onboarding nachts
  um 2 Uhr durchläuft, startet die Aufgabe um 2:00. Das entspricht seinem
  Lebensrhythmus, nicht einer geglätteten Idealzeit.

### 3.5 Visueller Onboarding-Hinweis an der Aufgabe

Damit die Onboarding-Aufgabe als das erkennbar ist, was sie ist — ein
Begleiter durch den Erststart, nicht eine vergessene alte Aufgabe — bekommt
sie einen dezenten visuellen Akzent.

**Vorschlag:** Ein kleines, zurückhaltendes Begleit-Icon (z.B. eine
stilisierte Kompass-Nadel, ein Sprössling, ein Welcome-Indikator) neben dem
Titel oder am rechten Rand der Aufgabenzeile in der Heute-Ansicht. Nicht
aufdringlich, eher als sanfte Markierung. Verschwindet, sobald die Aufgabe
abgeschlossen ist (oder der Nutzer sie aktiv editiert).

Das Icon ist *visuell*, nicht datenstrukturell. Es wird nicht durch ein
„onboarding"-Flag im Datenmodell getrieben, sondern durch eine UI-Heuristik:
Wenn eine Aufgabe aus einem Template stammt, das `isSampleData = true` hat,
und die Aufgabe selbst noch unverändert ist, wird das Icon angezeigt.

**Offene Detailfragen:**

- Welches Icon konkret?
- Soll es animieren, wenn der Nutzer die Heute-Ansicht zum ersten Mal sieht?
- Reagiert das Icon auf Interaktion (z.B. Tap zeigt eine kurze Erklärung
  „Das ist deine Onboarding-Aufgabe")?

### 3.6 Pflegeaufwand

Wenn die App neue Features bekommt — z.B. eine neue Klangwelt oder ein neuer
Aufgaben-Workflow —, sollte das Mustertemplate sich anpassen können, damit
Neuanwender den aktuellen Stand kennenlernen.

Bestandsnutzer haben das alte Template entweder unangetastet (wird
überschrieben) oder bereits modifiziert (wird nicht angetastet). Diese
Differenzierung ist die Hauptaufgabe von `isSampleData` (siehe 3.3).

Der Pflegeaufwand bleibt überschaubar, weil:

- nur ein Template gepflegt wird, nicht eine Vielzahl von Defaults
- Updates nur bei substantiellen App-Features nötig sind, nicht bei jeder
  Build-Iteration
- die Generation der Sample-Aufgabe automatisch der jeweils aktuellen
  Template-Version folgt

### 3.7 Offene Punkte

- Konkretes Icon für den Onboarding-Hinweis
- Texte der sechs Teilaufgaben
- Verhalten beim Update: Soll eine bereits generierte Sample-Aufgabe
  beim App-Update *auch* aktualisiert werden, oder bleibt nur die
  Template-Aktualisierung wirksam und neu generierte Aufgaben (z.B. durch
  manuelles „aus Template Aufgabe machen") nutzen den neuen Stand?

---

## 4. Zusammenspiel beider Features

Die zwei Features wirken zusammen am stärksten:

- **Default-Kategorien** geben dem Nutzer einen Rahmen, in den seine
  zukünftigen Aufgaben passen.
- **Mustertemplate + Sample-Aufgabe** geben ihm eine konkrete erste
  Handlung in diesem Rahmen — und lehren ihn dabei die Kernfunktionen.

Nach dem Onboarding sieht der Nutzer:

- in der Kategorienliste: vier farbige Kategorien mit Klangwelten
- in der Templates-Liste: ein Mustertemplate „MyCadenza einrichten"
- in der Heute-Ansicht: eine konkrete Aufgabe „MyCadenza einrichten" mit
  sechs Teilaufgaben, dezent als Onboarding-Begleiter markiert

Die App wirkt damit ab Sekunde eins bewohnt — und zeigt ihre Designsprache
in Aktion, nicht nur in Erklärtexten.

---

## 5. Build-Reihenfolge und Abhängigkeiten

```
Build 10721 (B-M2)            Import-Sperre, Sync-Erkennung entwickelt
        ↓
Build (folgt 10721)            Sync-aware Erst-Start-Erkennung extrahieren
        ↓
Build (Default-Kategorien)     Vier Kategorien anlegen, Klangwelt zuweisen
        ↓
Build (Mustertemplate)         Template anlegen mit isSampleData = true
        ↓
Build (Onboarding-Anpassung)   Screen 4 adaptiv, Sample-Aufgabe-Generation
        ↓
Build (Visueller Hinweis)      UI-Heuristik für Onboarding-Begleiter-Icon
```

Die genauen Build-Nummern werden bei Beginn der Etappe vergeben, abhängig
vom dann gültigen Stand der TodoList.

**Reihenfolge-Begründung:** Sync-Erkennung muss vor allem anderen stehen,
weil beide Features davon abhängen. Default-Kategorien vor Mustertemplate,
weil das Template eine Kategorie braucht. Onboarding-Anpassung kommt
danach, weil sie die zwei vorbereiteten Datenobjekte nur noch verbindet.
Der visuelle Hinweis ist die letzte Schicht — UI-Politur, sobald das
Datenmodell steht.

---

## Änderungsprotokoll

- **2026-04-28** — Erstentwurf, basierend auf Konzeptdiskussion vom 27. und 28.04.2026
