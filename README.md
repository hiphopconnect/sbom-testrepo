# sbom-testrepo

Test-Repo für den SBOM-Collector `sbom-collector`. `requirements.txt` enthält absichtlich alte Paketversionen mit bekannten Schwachstellen. Nichts davon wird installiert oder ausgeführt.

Bei jedem Push auf `main` erzeugt `.github/workflows/sbom.yml` mit Syft eine SBOM im Format CycloneDX. Haben sich die Abhängigkeiten geändert, bettet der Workflow Repo, Commit, Ref, Autor und Zeit ein und committet sie als `sbom.cdx.json` in dieses Repo. Der Collector holt sie täglich ab und schreibt den Bericht.

`.syft.yaml` schließt die eigene SBOM vom Scan aus, sonst läse Syft sie beim nächsten Lauf als Paketquelle mit.

## Einrichten

Settings > Actions > General > Workflow permissions: "Read and write permissions". Ein Secret ist nicht nötig.

Danach ein Paket in `requirements.txt` ändern und pushen. Nach dem Lauf liegt die neue `sbom.cdx.json` im Repo; den Bericht erzeugt der Collector bei seinem nächsten Lauf oder sofort über "Run workflow".

## Vorführung im Browser

Für die Vorführung liegen zwei vorbereitete Pull Requests bereit. `main` enthält die verwundbaren Versionen.

| Pull Request | Änderung | Erwartung im Collector |
|---|---|---|
| Abhängigkeiten auf aktuelle Versionen heben | alle Pakete auf den neuesten Stand | "geprüft", keine Befunde |
| Alle Abhängigkeiten entfernen | `requirements.txt` ohne Pakete | "SBOM leer", Lauf rot |

Ablauf für jede Probe:

1. **Ausgangslage zeigen:** im Collector `reports/SUMMARY.md` und den Bericht zu `sbom-testrepo` öffnen.
2. **Pull Request mergen:** Pull requests > PR öffnen > "Merge pull request". Damit landet die Änderung auf `main`.
3. **SBOM abwarten:** Actions > "SBOM erzeugen" läuft etwa 15 Sekunden. Danach zeigt der Commit "SBOM aktualisieren für …", dass sich die Abhängigkeiten geändert haben.
4. **Collector starten:** im Collector Actions > "SBOMs abholen und bewerten" > Run workflow > Run workflow. Etwa zwei Minuten.
5. **Ergebnis zeigen:** im Lauf den Schritt "Bewerten" öffnen (Zeilen `Positivkontrolle bestanden` und `<owner>/sbom-testrepo: …`), dann `reports/SUMMARY.md` und den Bericht.
6. **Zurücksetzen:** im gemergten Pull Request unten "Revert" > "Create pull request" > "Merge pull request". Damit stehen wieder die verwundbaren Versionen auf `main`; nach Schritt 3 und 4 zeigt der Bericht wieder die Befunde.

Die Pull Requests werden nacheinander vorgeführt, jeweils mit Zurücksetzen dazwischen. Ein gemergter Pull Request lässt sich nicht noch einmal mergen; für eine weitere Vorführung im Revert-Pull-Request wieder "Revert" wählen, das bringt die Änderung erneut als Pull Request.
