# Chess RNG mit Online-Warteschlange

Dieser Ordner ist fertig für **Firebase Hosting** im Projekt `chessrngreal`.
Es gibt **keine Anmeldung**: Jeder Browser bekommt automatisch eine zufällige Spieler-ID.

```
public/index.html   das komplette Spiel inkl. Online-Warteschlange
firestore.rules     Regeln für die Datenbank
firebase.json       Hosting- und Regel-Einstellungen
.firebaserc         verknüpft den Ordner mit dem Projekt chessrngreal
```

## 1. Einmalig in der Firebase-Konsole

**Firestore Database → Datenbank erstellen**
- Modus: Produktionsmodus
- Standort: z. B. `eur3 (europe-west)`

Authentication wird **nicht** gebraucht.

## 2. Firebase-Werkzeug installieren (einmalig)

Node.js installieren (nodejs.org, LTS), dann im Terminal:

```
npm install -g firebase-tools
firebase login
```

## 3. Eigene Adresse chessrng.web.app anlegen (einmalig)

Die Standard-Adresse eines Projekts ist immer die Projekt-ID (`chessrngreal.web.app`) und lässt sich nicht umbenennen. Eine zweite Website mit eigenem Namen geht aber. Im Terminal in diesem Ordner:

```
firebase hosting:sites:create chessrng
```

(Alternativ in der Konsole: Hosting → „Weitere Website hinzufügen“ → `chessrng`.)

Kommt die Meldung, dass der Name schon vergeben ist, gehört `chessrng` jemand anderem. Dann einen anderen Namen wählen, z. B. `chess-rng` oder `chessrng-game`, und denselben Namen in `firebase.json` bei `"site"` eintragen.

## 4. Hochladen

```
firebase deploy
```

Danach läuft das Spiel unter **https://chessrng.web.app**.

Die alte Adresse abschalten (optional):

```
firebase hosting:disable --site chessrngreal
```

## Rangliste

Der Reiter **Rangliste** zeigt die Top 50 nach Online-Siegen. Gezählt werden nur beendete Partien aus der Warteschlange. Die Regeln in `firestore.rules` sorgen dafür, dass jede Partie pro Spieler nur einmal gezählt wird und das Ergebnis zum Ausgang der Partie passen muss.

**Wichtig:** Nach diesem Update die Regeln neu veröffentlichen (`firebase deploy` macht das automatisch mit, oder den Inhalt von `firestore.rules` in der Konsole unter Firestore → Regeln einfügen).

## So funktioniert die Warteschlange

1. Spielen → **Online** → Name eingeben (optional), Loadout wählen → **Gegner suchen**.
2. Sucht gerade schon jemand, startet die Partie sofort. Sonst wartest du in der Warteschlange, bis der Nächste sucht.
3. Die Farbe wird ausgelost. Jeder spielt mit seinem eigenen Loadout, die Züge kommen live beim anderen an.
4. Ist der Gegner über eine Minute nicht mehr verbunden, kannst du den Sieg beanspruchen.

Zum Ausprobieren: die Seite in zwei Browsern öffnen (oder einmal normal und einmal im privaten Fenster) und in beiden „Gegner suchen“ drücken.

## Hinweise

- Ohne Anmeldung kann die Datenbank nicht prüfen, wer wer ist. Die Regeln begrenzen, welche Felder geändert werden dürfen, aber ein technisch versierter Spieler könnte schummeln. Für Ranglisten wäre später ein Server-Check (Cloud Functions) nötig.
- Beendete Partien bleiben in der Datenbank liegen. Wenn sich viele ansammeln, kann man in der Firestore-Konsole eine TTL-Regel (automatisches Löschen) für die Sammlung `games` anlegen.
