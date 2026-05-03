# MyCadenza — Roadmap v1.7.1 bis v2.x

> **Stand: 03.05.2026** · v1.7.0 zur Apple-Prüfung eingereicht · Roadmap für die nächsten 8-12 Monate als 7-Phasen-Plan mit Sicherheitsventilen

---

## Grundprinzip

Der Plan ist so aufgebaut, dass an jedem Phasen-Endpunkt ein **stabiler Zustand** erreicht ist. Wenn äußere Umstände oder Energielage einen Stop erfordern, bleibt das Produkt nutzbar und marktfähig — keine halbfertigen Brücken zur nächsten Phase. Das ist das wichtigste Architektur-Prinzip dieser Roadmap.

**Modus-Wechsel:** Diese Roadmap entstand in einer kreativen Phase am 03.05.2026. Nach Fertigstellung der Roadmap wechselt das Projekt in den Schritt-für-Schritt-Modus. Strategische Entscheidungen werden nur in expliziten Strategie-Sessions getroffen, nicht zwischen Build-Etappen.

---

## Phase 0 — Lessons Learned aus v1.7.0 (1 Stunde)

Vor v1.7.1 eine kurze Retrospektive. Was hat in v1.7.0 funktioniert (Hybrid mit Claude Code, Auftragsbriefe als md-Dateien, Doku-Repo als Single Source of Truth), was war zäh, was anders machen.

**Endpunkt:** Eine kurze Notiz im docs-Repo mit Erkenntnissen. Kein eigenständiges Dokument, sondern ein Anhang oder Abschnitt in dieser Roadmap.

---

## Phase 1 — v1.7.1 umsetzen (3-5 Wochen, intensive Arbeit)

Komplette Polierung der App auf Markttauglichkeit für die ersten echten User außerhalb des Bekanntenkreises.

**Inhalte:**

- **Datenmodell-Foundation:** Default-Kategorien (Haushalt, Arbeit, Family & Friends, Gesundheit) mit Klangwelt + Farbe + Icon. Mustertemplate „MyCadenza einrichten" mit `isSampleData`-Marker. Trigger-Logik: CloudKit-Initial-Sync abwarten, dann auf leere DB seeden.
- **Onboarding Screen 4 adaptiv:** Drei Varianten je nach Sync-Stand (keine Daten / Daten gefunden / Sync dauert).
- **Design-Konsolidierung:** TemplatePaletteReview, KlangweltReview (Cityflow + Horizont prüfen, ggf. neue Sounds), Hauptaufgaben-Design (Karten-Layout, Status-Indikatoren). Volle Farbharmonisierung über die App.
- **UX-Findings F12-F15:** F12 (UX-Detail aus 10720), F13 + F14 (TestFlight-Beobachtungen), F15 (Lautstärke-Differenzierung zu schwach, mit System-Klangwelt-Footer-Hinweis).
- **SubTaskEditor-Sheet Status-Picker** Position überdenken (Toolbar oder neben Speichern-Button als Alternativen).
- **Teilaufgaben-Lösch-Bestätigung:** Undo-Toast oder Confirmation-Dialog.
- **Akkordeon-Verhalten Hauptaufgaben:** Nur eine Teilaufgaben-Liste gleichzeitig offen (Beobachtung aus eigener Nutzung 03.05.2026).
- **Feinere Zeitstufen:** Validity-Duration und Grace-Period mit 5/15-Min-Werten erweitern, ggf. Hybrid (15 produktiv, 5 DEBUG-only).
- **Strict-Concurrency-Etappe:** SoundManager-bedingte @MainActor-Bezüge in den restlichen Code ziehen.

**Sicherheitsventil:** Wenn Energie ausgeht oder Kanzlei-Last hoch ist, kann die Phase verlängert werden. Stop nach Phase 1 ergibt eine deutlich verbesserte App, die sofort marktfähig ist.

**Endpunkt:** v1.7.1 in TestFlight, intern verifiziert, bereit für externe Beta-Tester.

---

## Phase 1.5 — Beta-Test mit externen Testern (2-3 Wochen)

Zwischen Phase 1 und Phase 2 eingeschoben — entstanden aus der Erkenntnis, dass „Family & Friends als Tester" unzuverlässig ist und echte Edge Cases von Nutzern aus der Zielgruppe kommen müssen.

**Inhalte:**

- 5-10 externe Tester aus der Zielgruppe (Berufstätige mit Tagesplanungs-Bedarf, vorzugsweise nicht aus dem unmittelbaren persönlichen Umfeld) gewinnen.
- TestFlight-Anschreiben formulieren mit klaren Test-Aufgaben (z.B. „Lege deine echten Aufgaben für eine Woche an und nutze die App täglich").
- Feedback-Sammelmethode etablieren (vermutlich Google Form oder eigene MyCadenza-Notiz, regelmäßiger Austausch).
- Erkenntnisse iterativ in die App einbauen, möglicherweise als 10727+-Builds.

**Sicherheitsventil:** Wenn keine externen Tester gefunden werden, kann diese Phase übersprungen oder verkürzt werden. Stop hier ergibt v1.7.1 als veröffentlichungsreif aus eigener Sicht.

**Endpunkt:** v1.7.1 als Release-Candidate, an App Store Connect übergeben.

---

## Phase 2 — Website ausbauen + Tutorials (3-4 Wochen, parallel zu Phase 3)

Marketing-Foundation für die ersten User. Mehr Aufwand als initial geschätzt — gute Tutorials sind eigenes Handwerk.

**Inhalte:**

- **Schicht 3 Anwenderdoku:** Quick-Start auf GitHub Pages, DE+EN.
- **Schicht 4 Anwenderdoku:** Vollständige Funktions-Doku, DE+EN, mit Screenshots aus der polierten v1.7.1.
- **Website-Sektionen aktualisieren:** Neue Screenshots aus v1.7.1, ggf. neue Texte über die Klangwelten-Identität, Demo-/Präsentations-Modus als Argument.
- **Eventuell erste Video-Tutorials** (Schicht 5) für die wichtigsten Funktionen — kann auch in spätere Phase rutschen.

**Sicherheitsventil:** Tutorials und Dokumentation können iterativ entstehen. Wenn nur Quick-Start fertig wird, ist das ein akzeptabler Phase-2-Endpunkt.

**Endpunkt:** Website mit aktualisierten Inhalten, mindestens Quick-Start verfügbar, Schicht 4 zumindest skeletal.

---

## Phase 3 — Erste 100 User erreichen (offene Dauer, 2-3 Monate realistisch)

Marketing-Phase. Eigenständig, ohne harte Zeitvorgabe — mit ständiger Erfolgs-Überprüfung.

**Acquisition-Pfade (Auswahl, nicht alle gleichzeitig):**

- **Reddit:** r/iosapps, r/productivity, r/digitalminimalism — wenn die Klangwelten-Idee dort gut ankommt.
- **Product Hunt Launch:** Zeitlich gut planen (Dienstag/Mittwoch morgens), kann in einem Tag 200-500 Visits bringen.
- **App Store SEO:** Keywords + Beschreibung optimieren. Langsam, aber nachhaltig.
- **Indie-iOS-Blogs:** Deutsche Indie-Blogs realistischer als Macworld o.ä.

**Bewusst NICHT in Phase 3:** Steuerberater-Kollegen-Netzwerk (würde Berufsstand-Bezug schaffen, der mit v1.7.1 noch nicht §203-fähig ist — heben wir uns für Phase 7 auf).

**Erfolgs-Überprüfung:** Regelmäßige Reflexion (alle 2-4 Wochen), ob der gewählte Acquisition-Pfad trägt. Wenn nicht, Pfad wechseln statt verdoppeln.

**Sicherheitsventil:** Phase 3 hat keine harte Erfolgsanforderung. „100 User" ist Zielmarke, nicht Pflicht. Stop nach Phase 3 mit z.B. 30 Usern und drei Reviews ist auch Erkenntnisgewinn.

**Endpunkt:** Erste echte User-Reviews vorhanden, Pricing-Diskussion auf Datenbasis möglich.

---

## Phase 3.5 — Atempause und Pricing-Entscheidung (2-3 Wochen)

Bewusste Pause zwischen Marketing und nächster großer Bauphase. Eingeführt, weil Solo-Entwickler-Burnout real ist und die Roadmap das nicht wiederholen darf.

**Inhalte:**

- App nur betreiben, nicht weiterbauen. Bugs fixen, beobachten.
- User-Feedback gründlich auswerten.
- Pricing-Entscheidung treffen: Bleibt v1.7.1 kostenlos? Wird sie kostenpflichtig? Wenn ja, wie viel? (Datenbasis aus echten User-Reaktionen, nicht aus Vermutungen.)
- Eigene Reflexion: Will ich Phase 4-7 wirklich machen, oder reicht der erreichte Stand?

**Sicherheitsventil:** Phase 3.5 ist explizit als „Stop-Möglichkeit" gestaltet. Wenn die App in diesem Zustand reicht, ist das eine valide Antwort. Es muss nicht weitergebaut werden.

**Endpunkt:** Klare Entscheidung, ob Phase 4-7 angegangen werden, und mit welcher Pricing-Strategie.

---

## Phase 4 — Backup-Format + Vapor-Server (7-10 Wochen, iterativ)

Foundation für v2.0. In zwei Sub-Phasen, die sich gegenseitig informieren werden.

**Phase 4a — Backup-Format und DTO-Schicht in der App (4-6 Wochen):**

- **Codable-DTOs** parallel zum SwiftData-Datenmodell.
- **Schemaversionierung-Header** im Backup-Format.
- **Verschlüsselungs-Layer** (User-Passwort-basiert, AES-256-GCM mit PBKDF2/Argon2-Schlüsselableitung).
- **Repository-Pattern**, das Local und iCloud sauber abstrahiert.
- **Backup-Export- und -Import-UI** in der App.
- **DSGVO-Datenexport-Funktion** (als Beifang erfüllt).

**Phase 4b — Vapor-Server aufsetzen (3-4 Wochen):**

- **Linux-Server-Setup** auf Hetzner Cloud (Cax21 oder größer).
- **Vapor-Projekt** mit denselben DTOs wie die App.
- **Auth-System** (E-Mail + Passwort, optional Sign in with Apple).
- **REST-API-Endpoints** für die Hauptobjekte (Tasks, Categories, Templates).
- **Sync-Protokoll** definieren (vermutlich Last-Write-Wins mit Timestamps).
- **Monitoring + Backup-Strategie** für den Server selbst.
- **TLS-Zertifikat** via Let's Encrypt.

**Iterativer Ansatz:** Server und Backup-Format informieren sich gegenseitig. Wenn am Server eine DTO-Struktur unhandlich ist, geht das zurück in die App-Schicht. Plan ist Wegweiser, nicht Korsett.

**Sicherheitsventil:** Phase 4a ist auch ohne Phase 4b nutzbar (Backup-Funktion in der App, manuelle Sicherung). Stop nach Phase 4a ergibt eine App mit verbessertem Datenmodell und Export-Funktion, die für Consumer ausreicht.

**Endpunkt:** Backup-Format funktioniert, Vapor-Server läuft, beide kommunizieren testweise.

---

## Phase 5 — Rechtliche Grundlagen (3-4 Wochen, ggf. parallel zu Phase 4b)

Sechs Aufgaben, nicht eine. Erfordert Anwalts-Konsultation.

**Inhalte:**

- **Privacy Policy aktualisieren** (mehrsprachig, juristisch tragfähig, Hetzner-spezifisch).
- **Auftragsverarbeitungsvertrag mit Hetzner** prüfen (Standard-AVV reicht für DSGVO, ggf. nicht für §203 — dann individuell verhandeln).
- **§203-spezifische Verpflichtungserklärung** für Hilfspersonen-Konstruktion (ggf. mit Anwalt formuliert).
- **AGB für den Pro-Plan** (Service-Level, Verfügbarkeit, Haftungsausschluss, Kündigungsrecht).
- **Notfall-Konzept** für Single-Point-of-Failure (technischer Treuhänder? dokumentierte Recovery-Prozesse?).
- **Impressum-Update** (gewerblicher Anbieter mit kontinuierlichem Service).

**Anwalts-Konsultation:** 2-3 Stunden bei spezialisierter IT-Recht/Berufsrecht-Kanzlei. Realistische Kosten 500-1500 € einmalig — im Verhältnis zum Risiko trivial.

**Iteration mit Phase 4b:** Rechtliche und technische Anforderungen verschränken sich. Eventuell muss das Backup-Format angepasst werden, wenn der Anwalt spezifische Audit-Trail-Anforderungen aus §203 aufzeigt.

**Sicherheitsventil:** Wenn die rechtliche Komplexität zu hoch wird, kann Phase 7 (Pro-Plan) verschoben oder gar nicht aktiviert werden. Phase 4 wäre dann ein „technischer Vorbau ohne Geschäftsmodell" — auch ein valider Endpunkt.

**Endpunkt:** Rechtssicherheit für Pro-Plan-Launch hergestellt, Kommunikations-Vorlagen für Website fertig.

---

## Phase 6 — Apple-Ökosystem-Features für beide Datenhaltungen (6-10 Wochen)

Die eigentlichen v2.0-Features. Funktionieren sowohl im iCloud- als auch im Hetzner-Modus.

**Inhalte:**

- **Aufgaben-Erstellung aus externen Quellen:** Kalendereintrag, E-Mail, Kontakt, Erinnerung, Messenger-Inhalt — als Alternative zu „Neu" und „Aus Template" in der EditorView.
- **ActionButtons in Aufgaben/Teilaufgaben:** Hyperlink, Kontakt-Aufruf, Termin-Aufruf, E-Mail-Composer, Telefonanruf, FaceTime, Weiterleitungs-Mail, neuer Termin.
- **Berechtigungs-UX:** Permission-Dialoge für EventKit, Reminders, Contacts mit Begründungstexten — idealerweise im Onboarding adressiert.
- **Quellen-Picker** für „Aus Kalendereintrag erzeugen".
- **Linked-Items-Datenmodell:** Stabile Identifier, Fallback-Strategie für gerätelokale Verlinkungen.
- **Auswirkung auf Backup-Größe** dokumentieren — Foto-Anhänge und Linked-Items vergrößern die Backups erheblich.

**Sicherheitsventil:** Features können einzeln entwickelt werden. Wenn z.B. EventKit-Integration zu komplex wird, kann sie auf v2.1 verschoben werden, während ActionButtons schon in v2.0 ausgeliefert werden.

**Endpunkt:** Feature-vollständige App, getestet in beiden Datenhaltungs-Modi.

---

## Phase 7 — Hetzner-Datenhaltung aktivieren + Pro-Plan launchen (2-4 Wochen)

Geschäftsmodell-Aktivierung. Erst nach Phase 6, weil Features vorher feature-fertig sein müssen.

**Inhalte:**

- **Pro-Plan-UI** in der App (Upgrade-Flow, Subscription-Management).
- **App-Store-Connect-Subscription-Setup** mit StoreKit-Integration.
- **Migration-Flow:** User wechselt von iCloud auf Hetzner, App macht Backup → Upload → Restore → Switch.
- **Pricing-Modell finalisieren** (basierend auf Erkenntnissen aus Phase 3.5).
- **Marketing-Welle für Pro-Plan:** Steuerberater-Kollegen-Netzwerk, BStBK-Mitgliederbereich, Steuerberaterverbände.
- **Bestandsuser-Schutz:** Erste 100 User aus Phase 3 bleiben kostenlos. Pro-Plan ist nur für Neuanmeldungen oder freiwillige Upgrades.

**Point of no return:** Nach Phase 7 hast du Kunden mit Verträgen. Service muss gehalten werden, kontinuierlicher Wartungsaufwand.

**Endpunkt:** Pro-Plan live, erste zahlende Kunden, kontinuierlicher Betrieb.

---

## Realistische Zeitachse — Gesamtschätzung

| Phase | Optimistisch | Realistisch | Pessimistisch |
|---|---|---|---|
| Phase 0 | 1 h | 1 h | 2 h |
| Phase 1 | 3 Wochen | 4-5 Wochen | 6-8 Wochen |
| Phase 1.5 | 2 Wochen | 2-3 Wochen | 4 Wochen |
| Phase 2 | 3 Wochen | 3-4 Wochen | 6 Wochen |
| Phase 3 | 6 Wochen | 8-12 Wochen | 16+ Wochen |
| Phase 3.5 | 2 Wochen | 2-3 Wochen | 4 Wochen |
| Phase 4 | 7 Wochen | 7-10 Wochen | 14 Wochen |
| Phase 5 | 3 Wochen | 3-4 Wochen | 6 Wochen |
| Phase 6 | 6 Wochen | 6-10 Wochen | 14 Wochen |
| Phase 7 | 2 Wochen | 2-4 Wochen | 6 Wochen |
| **Gesamt** | **~7 Monate** | **~9-12 Monate** | **~16-20 Monate** |

Realistisch: **9-12 Monate** vom heutigen Stand bis Phase 7 abgeschlossen, bei moderater Belastung neben Steuerberater-Tagesgeschäft.

---

## Sicherheitsventil-Übersicht — wo kann gestoppt werden?

| Stop nach | Was bleibt |
|---|---|
| Phase 1 | Polierte Tagesplaner-App, marktfähig, eigenständig |
| Phase 1.5 | App mit externem Beta-Feedback, Release-Candidate |
| Phase 2 | App + Website + Tutorials für Marketing |
| Phase 3 | Erste echte User, Reviews, Pricing-Datenbasis |
| Phase 3.5 | Bewusste Entscheidung: weiter oder stop |
| Phase 4a | App mit Backup-Funktion (DSGVO + manuelle Sicherung) |
| Phase 4b | Server-Foundation vorhanden, aber nicht aktiv für User |
| Phase 5 | Rechtliche Vorbereitung abgeschlossen, kein Pro-Plan-Druck |
| Phase 6 | Feature-vollständige App, beide Datenhaltungs-Modi getestet |
| Phase 7 | Pro-Plan live, kontinuierlicher Betrieb |

**Wichtig:** Bis einschließlich Phase 6 kann das Projekt jederzeit pausiert werden, ohne dass User Verträge haben oder Service erwarten. **Erst Phase 7 ist Point of no return.**

---

## Founder-Market-Fit als Erfolgsindikator

Eine Beobachtung aus dem 03.05.2026: Chris nutzt MyCadenza selbst als Werkzeug, nicht nur als Projekt. Aufgaben wie „MyCadenza" mit konkreten Teilaufgaben werden in der eigenen App gepflegt, das Eigen-Beobachtungs-Log läuft *innerhalb* der App. Das ist Founder-Market-Fit in Reinform und einer der zuverlässigsten Indikatoren für langfristiges Gelingen.

**Konsequenz:** Eine separate „User-Forschung" ist in den ersten Phasen weniger nötig, weil der Founder selbst aktiver Power-User ist. Erst ab Phase 1.5 (externe Tester) kommt der Blick von außen dazu.

---

## Burnout-Prävention als Architektur-Prinzip

Diese Roadmap ist explizit so gestaltet, dass kein Phase ein „Ich-muss-jetzt-durch"-Druck erzeugt. Jeder Phase-Endpunkt ist ein stabiler Zustand. Phase 3.5 ist als bewusste Atempause eingebaut. Sicherheitsventile sind nicht Notfall-Optionen, sondern legitime Endpunkte.

Hintergrund: Chris hat Burnout-Erfahrung (5-10 Jahre zurück) und hat daraus eine bewusste Methodik entwickelt — kreative Phasen nutzen, dann disziplinierter Schritt-für-Schritt-Modus, gezielte Auszeiten dazwischen. Diese Roadmap ist mit dieser Methodik kompatibel.

---

## Modus-Wechsel-Hinweis für Folgesessions

Diese Roadmap entstand in einer kreativen Phase. Sie ist *nicht* dafür gedacht, in jeder Folgesession diskutiert zu werden. Folgesessions sollten im Schritt-für-Schritt-Modus arbeiten:

- **Build-Etappe in der aktuellen Phase** (Auftragsbrief an Claude Code, Test, Commit, weiter).
- **Strategie-Sessions** nur in expliziten dafür reservierten Slots, idealerweise an Phasenübergängen.

Wenn in einer Build-Etappe strategische Fragen auftauchen, werden sie als Notiz hier in der Roadmap ergänzt und in der nächsten Strategie-Session behandelt — nicht spontan ausgetragen.

---

## Offene Punkte und Folge-Themen

- Tester-Anleitung-Vorlage und TestFlight-Anschreiben (in Phase 1.5 zu erstellen)
- Konkretes Feedback-Sammelmethoden-Tool wählen (Form, eigene App-Notizen, externes Tool)
- Acquisition-Pfad-Auswahl in Phase 3 (eine Strategie-Session vor Phase 3 sinnvoll)
- Anwalt für Phase 5 identifizieren — Empfehlung über IHK oder Steuerberaterkammer-Kontakte denkbar
- Schritt-für-Schritt-Anleitungen für Vapor-Server-Setup (Chris ist hier Lernender, braucht verifizierbare Einzelschritte)

---

*Dokument-Stand: 03.05.2026 · Erstversion · Wird in Folgesessions ergänzt und an realistische Erfahrungswerte angepasst.*
