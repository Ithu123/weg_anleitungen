# Anleitungen des Werkgymnasiums

Quelltext von <https://werkgymnasium.eu/> — den Anleitungen für die
digitalen Dienste der Schule.

**Diese Website hat bewusst keine Anmeldung.** Wer seine Zugangsdaten vergessen
hat, kann sich nicht anmelden, um nachzulesen, wie man sie zurücksetzt. Daraus
folgt die einzige Regel für den Inhalt:

> Hier steht nichts, was jemanden gefährdet. Keine internen Adressen, keine
> Servernamen, keine Hinweise auf den Aufbau der Anlage.

Das gehört in die Betriebsdoku unter `betrieb.werkgymnasium.eu`, und die liegt
hinter dem SSO.

## Lokal bauen

```bash
python3 -m venv .venv-docs
.venv-docs/bin/pip install -r requirements-docs.txt

.venv-docs/bin/zensical serve    # http://localhost:8000
.venv-docs/bin/zensical build    # nach site/
```

> **Wenn `Defekter Interpreter` kommt, wurde das Verzeichnis verschoben oder
> umbenannt.** Ein venv speichert **absolute Pfade** in den Shebangs seiner
> Skripte; nach einem Umzug zeigen sie ins Leere. `python3 -m venv` repariert
> das *nicht* — es sieht ein vorhandenes Verzeichnis und lässt die alten
> Skripte stehen. Wegwerfen und neu bauen:
>
> ```bash
> rm -rf .venv-docs
> python3 -m venv .venv-docs
> .venv-docs/bin/pip install -r requirements-docs.txt
> ```
>
> Es geht dabei nichts verloren — das venv ist ein Bauartefakt und steht
> in `.gitignore`.

## Wie es ins Netz kommt

Ein Push auf `main` löst die Action aus. Sie baut die Website und legt das
Ergebnis im Branch `site` unter `www/` ab. Der Server `weg1` holt es von dort
alle fünf Minuten per Timer — er baut nichts selbst und sieht nur fertiges HTML.

Die Ansible-Seite steht im Repo `weg`: `roles/website` und
`playbooks/websites.yml`.

## Eine Seite hinzufügen

1. Markdown-Datei unter `docs/` anlegen.
2. In `zensical.toml` unter `nav` eintragen — sonst taucht sie nicht auf.

## Screenshots

Unter **`docs/bilder/`** liegen graue PNG-Platzhalter mit der Aufschrift
*„Screenshot fehlt“* — je einer für jeden Schritt, der ein Bild braucht. Sie
sind dazu da, ersetzt zu werden: echten Screenshot machen, Platzhalterdatei
**unter demselben Namen** überschreiben, fertig. In den Anleitungen muss dann
nichts geändert werden.

> [!WARNING]
> **Nicht nach `site/bilder/` speichern.** Das Verzeichnis sieht genauso aus,
> ist aber Bauergebnis: `zensical build` *und* `zensical serve` löschen `site/`
> zu Beginn jedes Laufs vollständig und ohne Rückfrage. Was dort liegt und
> sonst nirgends, ist beim nächsten Aufruf weg — und weil `site/` in
> `.gitignore` steht, hat git es nie gesehen. Quelle ist ausschließlich `docs/`.

Deshalb sind die Platzhalter PNG und nicht SVG: Ein Screenshot ist ein PNG, und
nur bei gleicher Endung stimmt der Verweis in der Markdown-Datei hinterher noch.

Auf den Bildern darf nichts stehen, was nicht auch im Text stehen dürfte: keine
echten Namen, keine fremden Unterhaltungen, keine internen Adressen.

Bei den Anmelde-Screenshots ist das vor allem eine Frage der **Browserleiste**.
Sobald der Vorgang beim Anmeldedienst angekommen ist, steht dort dessen
Hostname — genau das, was nach der Regel oben hier nicht stehen soll. Deshalb
bei diesen Schritten die Leiste **wegschneiden** und nur das Formular zeigen;
das ist einfacher als schwärzen und ergibt bessere Bilder.

Umgekehrt beim allerersten Bild: Dort **gehört** die Adresse
`element.werkgymnasium.eu` sichtbar ins Bild. Sie bringt den Leuten bei, vor
der Eingabe des Schulkontos auf die Adresse zu schauen — der einzige Schutz
gegen eine nachgebaute Anmeldeseite.

Die Parameter in der Adresse (`state`, `nonce`, `client_id`) sind keine
Geheimnisse und müssen nicht geschwärzt werden. Das einmalige `code=`
beziehungsweise `loginToken=` wäre eines, steht aber nur während der
Weiterleitung da, wird sofort eingelöst und ist danach verbraucht.

Am sichersten ist ein **Testkonto** mit erfundenen Räumen: Dann gibt es nichts
zu schwärzen. Muss doch geschwärzt werden, gilt: kein Weichzeichnen, kein
Verpixeln — beides ist rückrechenbar — sondern deckende Flächen, danach neu
kodieren und Metadaten entfernen:

```bash
magick roh.png -fill black -draw "rectangle 350,180 1040,220" - \
  | magick - -background white -flatten -strip zensiert.png

# Pruefen: Farben im geschwaerzten Bereich muss 1 sein
magick zensiert.png -crop 690x40+350+180 +repage -format "%k\n" info:
```

Ein halbdurchsichtiger Balken sieht am Bildschirm schwarz aus und gibt den Text
trotzdem preis; die Zahl `1` ist der Beweis, das Auge nicht.
