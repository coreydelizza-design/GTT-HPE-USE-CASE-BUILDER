# GTT + HPE — Strategic Use Case Tool

Field-enablement asset: a single self-contained HTML tool presenting two industry
use cases (QSR retail, global manufacturing) on the GTT + HPE Unified SASE blueprint.

## Repo layout

```
docs/                              ← repo documentation (NOT served)
  gtt-hpe-use-cases-tech-arch.md   ← technical architecture (proposal)

gtt-hpe-deploy/                    ← the served root — Railway serves THIS folder
  index.html                       ← the tool
  package.json                     ← declares `serve` + start script
  railway.json                     ← pins start command + restart policy
  README.md                        ← deploy steps (GitHub / Railway / Teams)
  .gitignore
```

## Important: keep docs out of the served root

`docs/` is a **sibling** of `gtt-hpe-deploy/`, not inside it. The Markdown docs are
never served, executed, or referenced by `index.html` — they have zero impact on the
rendered UI, and they are not reachable by URL on the live site.

When configuring Railway, set the service **Root Directory** to `gtt-hpe-deploy`
(Settings → Root Directory). That way Railway builds/serves only the deploy folder
and never sees `docs/`.

## Deploy

See `gtt-hpe-deploy/README.md` for click-by-click GitHub → Railway → Teams steps.

## Architecture

See `docs/gtt-hpe-use-cases-tech-arch.md`.
