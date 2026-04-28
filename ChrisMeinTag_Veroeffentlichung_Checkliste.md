# ChrisMeinTag – Checkliste App-Veröffentlichung

## 1. Xcode-Projekt vorbereiten

- [ ] **Bundle Identifier** prüfen: `com.hoferer.ChrisMeinTag` (Target → General)
- [ ] **Version** setzen: z.B. `1.0` (Target → General → Version)
- [ ] **Build Number** setzen: z.B. `1` (Target → General → Build)
- [ ] **Display Name** setzen: "ChrisMeinTag" (Target → General)
- [ ] **Deployment Target** prüfen: iOS/iPadOS 17.0 (Target → General)
- [ ] **Device Orientation** prüfen: Portrait (Target → General → Deployment Info)
- [ ] **App Category** prüfen: Productivity (Target → General)

## 2. App Icon

- [ ] **1024×1024 App Icon** vorhanden in Assets.xcassets → AppIcon
- [ ] Kein Alphakanal (App Store lehnt Icons mit Transparenz ab)
- [ ] Icon sieht auf hellem und dunklem Hintergrund gut aus

## 3. Launch Screen

- [ ] LaunchBackground Color Set in Assets.xcassets vorhanden (#C8973A)
- [ ] Info.plist: UILaunchScreen → UIColorName = "LaunchBackground"
- [ ] SplashLogo Image Set in Assets.xcassets vorhanden

## 4. Code-Hygiene

- [ ] Alle `#if DEBUG`-Sections prüfen (z.B. "TEST: Als gestern erledigt markieren")
- [ ] Keine Test-Daten oder Dummy-Inhalte im Code
- [ ] Keine `print()`-Statements die sensible Daten ausgeben
- [ ] Clean Build: ⌘⇧K dann ⌘B – keine Warnings, keine Errors
- [ ] Auf echtem Gerät testen (iPhone UND iPad)

## 5. Capabilities prüfen

- [ ] iCloud: CloudKit mit Container `iCloud.com.hoferer.cmt`
- [ ] Background Modes: Background fetch + Remote notifications
- [ ] Push Notifications: aktiviert
- [ ] Entitlements-Datei korrekt (APS Environment, iCloud Container, CloudKit)

## 6. App Store Connect vorbereiten

### Account
- [ ] Apple Developer Program Mitgliedschaft aktiv (99$/Jahr)
- [ ] Bei App Store Connect eingeloggt (appstoreconnect.apple.com)

### App anlegen
- [ ] Neue App in App Store Connect erstellen (falls noch nicht geschehen)
- [ ] Bundle ID: `com.hoferer.ChrisMeinTag` auswählen
- [ ] Primärsprache: Deutsch
- [ ] SKU: z.B. `chrismeintag001`

### Metadaten (für App Store Listing)
- [ ] **App Name**: ChrisMeinTag
- [ ] **Untertitel**: z.B. "Dein Tagesplaner" (max. 30 Zeichen)
- [ ] **Beschreibung**: Was die App macht, Hauptfeatures (max. 4000 Zeichen)
- [ ] **Keywords**: z.B. "Aufgaben,Tagesplan,Routine,Planer,Todo" (max. 100 Zeichen)
- [ ] **Support-URL**: Webseite oder z.B. eine einfache GitHub-Page
- [ ] **Datenschutzrichtlinie-URL**: Erforderlich – kann eine einfache Seite sein
- [ ] **Kategorie**: Produktivität

### Screenshots (erforderlich)
- [ ] **iPhone 6.7"** (iPhone 15 Pro Max): mindestens 1, empfohlen 3-5
- [ ] **iPad 12.9"** (iPad Pro): mindestens 1, empfohlen 3-5
- [ ] Screenshots zeigen die Hauptfunktionen: Heute-Ansicht, Editor, Kategorien
- [ ] Formate: PNG oder JPEG, im Simulator erstellen (⌘S)

### App Review Information
- [ ] Kontaktdaten für das Review-Team hinterlegen
- [ ] Hinweis bei Review Notes: "App is intended for unlisted distribution" (falls Unlisted gewünscht)

## 7. Archiv erstellen und hochladen

### In Xcode
1. [ ] Build-Ziel auf **"Any iOS Device (arm64)"** setzen (nicht Simulator)
2. [ ] **Product → Archive**
3. [ ] Warten bis das Archive im Organizer erscheint
4. [ ] **Distribute App → App Store Connect → Upload**
5. [ ] Signing: "Automatically manage signing" belassen
6. [ ] Upload abwarten – Xcode zeigt Erfolg oder Fehler

### In App Store Connect
7. [ ] Unter "TestFlight" prüfen ob der Build erscheint (kann 5-15 Min dauern)
8. [ ] Build wird automatisch von Apple verarbeitet (Processing)
9. [ ] Falls "Missing Compliance": Export Compliance auf "No" setzen (keine Verschlüsselung über HTTPS hinaus)

## 8. TestFlight einrichten

### Interne Tester (sofort, ohne Review)
- [ ] Unter TestFlight → "Interne Tests" eine Gruppe erstellen
- [ ] Eigenen Account und weitere Team-Mitglieder hinzufügen
- [ ] Build der Gruppe zuweisen
- [ ] TestFlight-App auf den Geräten installieren

### Externe Tester (nach Beta-Review durch Apple)
- [ ] Unter TestFlight → "Externe Tests" eine Gruppe erstellen
- [ ] Tester per E-Mail einladen
- [ ] Beta-Testinformationen ausfüllen (kurze Beschreibung, was getestet werden soll)
- [ ] Build zur externen Gruppe hinzufügen → löst Beta App Review aus
- [ ] Review dauert meist 24-48 Stunden

## 9. Nach dem Upload prüfen

- [ ] App startet korrekt aus TestFlight
- [ ] iCloud-Sync funktioniert zwischen Geräten
- [ ] Erinnerungen/Notifications kommen an
- [ ] Kategorien, Templates, Teilaufgaben funktionieren
- [ ] Launch Screen wird korrekt angezeigt
- [ ] Keine Crashes in der TestFlight-Konsole

## 10. Späterer Schritt: Unlisted Distribution (optional)

Falls du die App nur per Link teilbar machen willst (nicht öffentlich suchbar):
- [ ] App muss zuerst durch das normale App Store Review
- [ ] Unlisted-Antrag in App Store Connect stellen
- [ ] Nach Genehmigung: Direktlink wird generiert
- [ ] Achtung: Unlisted kann nicht rückgängig gemacht werden
- [ ] Apple empfiehlt eine Authentifizierung in der App

---

**Hinweis:** Die häufigsten Ablehnungsgründe beim App Review sind:
fehlende Datenschutzrichtlinie, Crashes beim Review, fehlende Screenshots,
und unvollständige Metadaten. Die Checkliste oben deckt alles ab.
