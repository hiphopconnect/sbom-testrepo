# sbom-testrepo

Test-Repo für [sbom-collector](https://github.com/hiphopconnect/sbom-collector). `requirements.txt` enthält absichtlich alte Paketversionen mit bekannten Schwachstellen. Nichts davon wird installiert oder ausgeführt.

Bei jedem Push auf `main` erzeugt `.github/workflows/sbom.yml` mit Syft eine SBOM im Format CycloneDX. Haben sich die Abhängigkeiten geändert, bettet der Workflow Repo, Commit, Ref, Autor und Zeit ein und committet sie als `sbom.cdx.json` in dieses Repo. Der Collector holt sie täglich ab und schreibt den Bericht.

`.syft.yaml` schließt die eigene SBOM vom Scan aus, sonst läse Syft sie beim nächsten Lauf als Paketquelle mit.

## Einrichten

Settings > Actions > General > Workflow permissions: "Read and write permissions". Ein Secret ist nicht nötig.

Danach ein Paket in `requirements.txt` ändern und pushen. Nach dem Lauf liegt die neue `sbom.cdx.json` im Repo; den Bericht erzeugt der Collector bei seinem nächsten Lauf oder sofort über "Run workflow".
