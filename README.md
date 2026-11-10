# browser-extension-updates

Oeffentlicher Versions-Feed fuer eigene Browser-Extensions. Enthaelt **keinen Quellcode**,
nur die jeweils neueste Versionsnummer je Extension, damit die Extension selbst (im
Hintergrund-Script) per `fetch` erkennen kann, dass eine neuere Version existiert - ganz
ohne Chrome Web Store.

`registry.json` wird nicht manuell gepflegt, sondern automatisch per GitHub Actions aus
dem jeweiligen Extension-Repo aktualisiert (siehe unten).

## Format (`registry.json`)

```json
{
  "extensions": {
    "<slug>": {
      "version": "1.2.3",
      "url": "https://github.com/<owner>/<repo>",
      "notes": ["Kurzbeschreibung Aenderung 1", "Kurzbeschreibung Aenderung 2"]
    }
  }
}
```

- `<slug>`: eindeutiger Kurzname der Extension (z.B. `better-ramp`).
- `version`: wird automatisch aus der `manifest.json` des Extension-Repos uebernommen.
- `url`: Link, der im Update-Hinweis der Extension angezeigt wird.
- `notes`: optionale Liste von Stichpunkten fuer die Anzeige im Popup.

## Automatisierung (Push-getriggert)

Jedes Extension-Repo (z.B. `BetterRamp`) enthaelt einen Workflow
(`.github/workflows/publish-version.yml`), der bei jedem Push auf `main` mit Aenderung an
`manifest.json` automatisch:

1. die `version` aus der eigenen `manifest.json` liest,
2. per GitHub-API den passenden Eintrag in diesem Repo (`registry.json`) aktualisiert.

Voraussetzung im Extension-Repo: ein Repository-Secret `UPDATES_REPO_TOKEN`
(Fine-grained PAT mit `Contents: Read and write` nur auf dieses Repo hier), damit der
Workflow schreibend zugreifen darf.

## Neue Extension hinzufuegen

1. In diesem Repo passiert nichts manuell - der erste erfolgreiche Workflow-Lauf der neuen
   Extension legt den `slug`-Eintrag in `registry.json` automatisch an bzw. aktualisiert ihn.
2. Im neuen Extension-Repo den Workflow aus `BetterRamp/.github/workflows/publish-version.yml`
   kopieren, `SLUG` und `EXTENSION_URL` anpassen, Secret `UPDATES_REPO_TOKEN` hinterlegen.
