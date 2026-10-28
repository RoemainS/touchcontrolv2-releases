# TouchControlv2 releases

Releasebron voor de automatische updates van **TouchControlv2**.

De app controleert maximaal eenmaal per dag of er een nieuwere versie is. Dat
gebeurt door één bestand uit deze repo te lezen: [`latest.json`](latest.json).
Is er een nieuwere versie, dan toont de app een melding en kan de gebruiker de
update met één klik installeren.

De app leest de feed via:

```text
https://raw.githubusercontent.com/RoemainS/touchcontrolv2-releases/main/latest.json
```

## Structuur

| Onderdeel            | Waar                                  | Waarom                                     |
| -------------------- | ------------------------------------- | ------------------------------------------ |
| `latest.json`        | In de repo (`main`)                   | Kleine tekstfile; de feed die de app leest |
| `TouchControlv2-setup-v<versie>.exe` | Als **Release-asset** | Grote binary hoort niet in git             |

## Formaat van `latest.json`

```json
{
  "version": "2.0.11",
  "url": "https://github.com/RoemainS/touchcontrolv2-releases/releases/download/v2.0.11/TouchControlv2-setup-v2.0.11.exe",
  "sha256": "<sha256-van-de-installer>",
  "releaseDate": "2026-10-28",
  "notes": "Korte releasenotes die in de melding verschijnen."
}
```

| Veld          | Omschrijving                                                     |
| ------------- | ---------------------------------------------------------------- |
| `version`     | Versienummer, bv. `2.0.11` (numeriek vergelijkbaar)              |
| `url`         | Download-URL van de installer (de Release-asset van deze repo)   |
| `sha256`      | SHA-256-hash van de installer; de app weigert bij een mismatch   |
| `releaseDate` | Releasedatum `YYYY-MM-DD`                                        |
| `notes`       | Korte releasenotes (getoond in de updatemelding)                 |

> **Belangrijk:** `url` en `sha256` moeten bij elkaar horen. De hash wordt
> automatisch berekend door `build-release.ps1` in de TouchControlv2-hoofdrepo.

## Een nieuwe versie uitbrengen

1. **Bouwen** in de TouchControlv2-hoofdrepo (zet eerst het nieuwe versienummer
   in `version.txt`):

   ```powershell
   .\build-release.ps1 -Notes "Wat er nieuw is"
   ```

   Dit publiceert de app, bouwt de installer, berekent de SHA-256 en genereert
   een `latest.json` (met de juiste GitHub-download-URL al ingevuld).

2. **Release aanmaken** in deze repo:
   - Maak een nieuwe Release met tag `v<versie>` (bv. `v2.0.11`).
   - Upload `TouchControlv2-setup-v<versie>.exe` als asset.

3. **Feed bijwerken**:
   - Vervang `latest.json` in deze repo (branch `main`) door de nieuw
     gegenereerde versie (via de website of een commit).

Binnen een dag zien alle installaties de nieuwe versie.

## Terugdraaien

Een update terugdraaien kan door `latest.json` terug te zetten naar een eerdere
versie (met de bijbehorende `url` en `sha256`). Zolang de oude Release-asset
blijft bestaan, blijft die download werken.

## Opmerkingen

- `raw.githubusercontent.com` heeft een CDN-cache van enkele minuten; omdat de
  app maar eenmaal per dag controleert, is dat geen probleem.
- Alleen HTTPS wordt gebruikt en elke download wordt op SHA-256 gecontroleerd.
- De feed-URL is per installatie instelbaar via `UpdateFeedUrl` in
  `%AppData%\TouchControlv2\TouchControlConfig.json`. Auto-update kan uit met
  `EnableAutoUpdateCheck: false`.
