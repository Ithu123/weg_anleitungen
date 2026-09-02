# Anleitungen der Werkgymnasium

Quelltext von <https://anleitungen.werkgymnasium.eu/> — den Anleitungen für die
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

## Wie es ins Netz kommt

Ein Push auf `main` löst die Action aus. Sie baut die Website und legt das
Ergebnis im Branch `site` unter `www/` ab. Der Server `weg1` holt es von dort
alle fünf Minuten per Timer — er baut nichts selbst und sieht nur fertiges HTML.

Die Ansible-Seite steht im Repo `weg`: `roles/website` und
`playbooks/websites.yml`.

## Eine Seite hinzufügen

1. Markdown-Datei unter `docs/` anlegen.
2. In `zensical.toml` unter `nav` eintragen — sonst taucht sie nicht auf.
