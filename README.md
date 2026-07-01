# GTT + HPE — Field Enablement Kit

One landing hub linking three self-contained static tools for the GTT + HPE
Unified SASE deal motion.

## Repo layout

```
site/                       ← the served root (Railway serves THIS folder)
  index.html                ← landing hub, links the three tools
  use-cases.html            ← 01 · Strategic Use Cases (present / export)
  live-guide.html           ← 02 · Live Conversation Guide (during the call)
  profile-builder.html      ← 03 · Use Case Profile Builder (leave-behind)
  .gitignore

docs/                       ← documentation (NOT served)
  gtt-hpe-use-cases-tech-arch.md

package.json                ← declares `serve`, serves site/
railway.json                ← pins the start command
README.md                   ← this file
```

## The three tools

| # | Tool | When a rep uses it |
|---|------|--------------------|
| 01 | Strategic Use Cases | Prep and in the room — present the blueprint, use cases, ICPs, why GTT+HPE, sales play |
| 02 | Live Conversation Guide | During a qualified call — talk tracks, discovery, objection reopeners, tap-to-capture, PDF summary |
| 03 | Use Case Profile Builder | After the call — populate a vertical for the account, export a tailored PDF |

All three are single static HTML files: no build, no backend, no database,
dependency-free (PDF export uses the browser's native print). Content mixes
GTT/HPE-sourced material (provenance-tagged) with original Solution Architecture
work — see each tool's in-page sources notes and `docs/`.

## Deploy on Railway

1. Push everything in this folder to a GitHub repo (repo root = this folder).
2. Railway → New Project → Deploy from GitHub repo → pick it.
3. Railway installs `serve` and runs `npx serve -s site -l $PORT`.
4. Settings → Networking → Generate Domain. That URL is the hub.

Because the start command serves `site/`, the `docs/` folder is never exposed —
no extra Railway root-directory setting needed.

## Microsoft Teams

Add a channel tab → Website → paste the Railway hub URL. It renders inline;
reps reach all three tools from one tab.

## Notes for reviewers

- Fonts load from Google Fonts over the internet — fine on Railway; may fall back
  to system fonts inside a Google-Fonts-blocking corporate environment.
- The live guide's talk tracks are suggested language pending a claims/enablement
  review before reps rely on them verbatim.
- Everything is stateless: nothing is saved or sent; PDF export and call summaries
  are generated in-browser from in-memory state and reset on reload.
