gtt-hpe-use-cases — Technical Architecture

Status: Proposal (delivery layer for the GTT + HPE Unified SASE solution architecture). Cross-refs: `gtt-hpe-solution-overview-tech-arch.md` (the solution being sold, once an overview doc exists), `_TEMPLATE-tech-arch.md` (house style).

Scope: This doc covers the **field-enablement asset** — a single self-contained HTML tool that presents two industry use cases (QSR retail, global manufacturing) on the GTT + HPE blueprint, plus its hosting/deploy path (GitLab → Railway, or Teams embed). It deliberately leaves the *underlying network/security solution architecture* (SD-WAN, SSE, ZTNA topology, PoP design, policy engine) to a separate overview doc — this asset *describes* that solution for a sales audience; it does not implement it. Diagrams below (content flow, decision logic, deploy topology) are primary content.

---

## 1. Purpose in one paragraph

The tool is a sales-engagement builder, not a pricing calculator: a GTT seller or SE opens it in a meeting (or forwards a link), walks a prospect from a shared architectural thesis, into their own industry's problem, through the resulting business shift, and only then into the GTT+HPE mechanism — problem before technology by design. A reader lands on the Blueprint (five SASE principles), switches to their vertical under Use Cases, absorbs a provocation + before/after that deepens the pain, sees the outcome shift, then the reference architecture, complementary GTT services, GTT.net proof points, and an industry ICP; internal readers also get a Why-GTT+HPE differentiation section and a Sales Play with discovery questions and objection reopeners. Mechanically it is one static `index.html` — vanilla HTML/CSS with a single ~15-line vanilla-JS tab switcher, Google Fonts over CDN, no framework, no backend, no datastore, served static on Railway.

---

## 2. What we're solving

The GTT+HPE partnership had a white paper and no repeatable field motion. Reps improvised the story per-meeting; the "why this partnership" beat lived in nobody's slide; and any collateral that existed mixed sourced facts with unreviewed claims invisibly. This asset makes the motion repeatable, sequenced, and provenance-auditable.

| Problem | Symptom | Fix in this doc |
|---|---|---|
| No repeatable motion | Every rep rebuilds the GTT+HPE story from the white paper per meeting; inconsistent framing across regions | Single tool encoding a fixed funnel (Blueprint → Use Cases → Why → Sales Play) any rep can walk |
| Product-led drift | Collateral opens on the SD-WAN/SSE stack before the customer's problem is established; loses the room | Enforced problem→technology order: scenario + stakes + outcome shift *precede* the architecture in every use case |
| Generic pitch | One-size deck doesn't fit a 200-store QSR and a 40-plant manufacturer; rep skips irrelevant content live | Two industry archetypes behind a tab switcher; rep presents only the prospect's vertical |
| "Why not a point vendor" unanswered | Differentiation was distributed across architecture layers; no concentrated answer when a competitor is in the room | Dedicated Why-GTT+HPE section (convergence + backbone + single-source), positioned after "this is you," before "how to buy" |
| Objection death | Deals stall on "we already have SASE" / "too disruptive" / "buy direct"; rep rebuts and closes the conversation | Objection *reopeners* — each answered with a discovery question, not a rebuttal |
| Claim risk | Sourced facts and original strategic assertions were indistinguishable; nothing safe to publish without full re-review | In-tool Sources & provenance block: green = GTT/HPE-sourced, purple = Solution Architecture (needs claims-owner sign-off) |

Each layer is independently valuable; they compose into one asset.

---

## 3. System context / architecture

Diagrams are primary content. This is a static asset, so the "system" is the content funnel plus the thin delivery/runtime around it.

```
 CONTENT FUNNEL  (the argument the tool encodes — top nav order)
 ─────────────────────────────────────────────────────────────────
   [ BLUEPRINT ]      shared thesis      → 5 SASE principles (vendor-neutral)
        │
        ▼
   [ USE CASES ]      "this is you"      → tab: QSR  |  tab: MANUFACTURING
        │                                   each vertical, self-contained:
        │                                   problem → stakes → shift → mechanism
        │                                            → services → proof → ICP
        ▼
   [ WHY GTT+HPE ]    "why us"           → convergence + backbone + single-source
        │
        ▼
   [ SALES PLAY ]     "how we win"       → 5-phase motion + discovery + objection reopeners
        │
        ▼
   [ SOURCES ]        provenance         → green (sourced)  vs  purple (Solution Arch)

 PER-USE-CASE ORDER  (problem before technology — enforced)
 ─────────────────────────────────────────────────────────────────
   Industry profile  →  Stakes (provocation + today/after)  →  The shift (outcomes)
        →  Reference architecture  →  Complementary services  →  Proof  →  ICP
      └────────── PROBLEM ──────────┘   └── RESULT ──┘        └──── MECHANISM ────┘
```

```
 RUNTIME / DELIVERY
 ─────────────────────────────────────────────────────────────────
   Browser ──HTTP──▶ Railway static service ( npx serve -s . )
      │                                          └─ serves index.html
      ├──CDN──▶ fonts.googleapis.com  (Archivo, IBM Plex Mono)
      └──click──▶ gtt.net / gtt.net/resources  (proof + service links, target=_blank)

   Alt delivery:  Teams "Website" tab ──iframe──▶ same Railway URL
```

Mental model: the tool is a **read-only, single-request artifact**. One GET returns the whole experience; every interaction after that (tab switch, anchor nav) is client-side. The only outbound dependencies are cosmetic (fonts) and referential (gtt.net links) — neither is on the critical path to the content rendering.

Trust-boundary crossings: (a) browser → Railway (serves our HTML), (b) browser → Google Fonts CDN (styling only), (c) browser → gtt.net (user-initiated navigation only, new tab). No inbound data, no auth, no PII, no write path.

---

## 4. Per-layer detail

This doc owns the delivery asset. The solution components it *depicts* (rows marked "depicted") are owned by the solution overview doc, not here.

| # | Component | Where | Replicas | Storage | Purpose |
|---|---|---|---|---|---|
| 1 | `index.html` | Railway static service | 1 | none | Entire tool: content, styles, tab JS |
| 2 | `serve` (npm) | Railway (Nixpacks build) | 1 | none | Static file server; binds `$PORT` |
| 3 | Google Fonts | CDN (third-party) | — | none | Archivo / IBM Plex Mono webfonts |
| 4 | gtt.net links | third-party (GTT web) | — | none | Proof points + service pages (referential) |
| 5 | GTT+HPE stack | *depicted, not built here* | — | — | SD-WAN/SSE/ZTNA/backbone — see overview doc |

Owned here: rows 1–2. Shared-platform: Railway itself. Third-party: rows 3–4. Out of scope for this doc: row 5.

---

## 5. Dataflow / the interesting flow

The "interesting flow" is not a request — it's the **rep's traversal** and the one piece of client-side logic (tab switching). Runtime dataflow is trivial (one static GET); the engagement logic is the substance.

```
 1. GET /                → Railway returns index.html (single document, ~68 KB)
 2. Browser paints       → sticky top nav + hero + all sections present in DOM
 3. Fonts resolve async  → (a) CDN reachable  → Archivo/IBM Plex render
                           (b) CDN blocked     → system-font fallback (see §9)
 4. Rep/prospect acts:
      (a) clicks a top-nav anchor → smooth-scroll to section (CSS, no JS)
      (b) clicks a Use Case tab   → JS toggles .active on tab + panel
      (c) clicks a proof/service link → gtt.net opens in new tab (target=_blank)
 5. No further server contact. Entire session is one GET + client-side state.
```

Decision tree — which use-case panel renders

```
 Use Case tab clicked
   │
   ├─ data-uc == "qsr"  ──▶ remove .active from all tabs+panels
   │                         add .active to QSR tab + #uc-qsr panel
   │                         (manufacturing panel: display:none)
   │
   └─ data-uc == "mfg"  ──▶ remove .active from all tabs+panels
                             add .active to MFG tab + #uc-mfg panel
                             (QSR panel: display:none)

 Print / no-JS fallback ──▶ CSS @media print forces BOTH panels visible
                             (rep can print or PDF the full asset)
```

The no-JS/print branch matters: if the tab JS never executes (locked-down browser, print-to-PDF), the CSS print rule reveals both panels so no content is lost — the tool degrades to a full linear document.

---

## 6. Storage tiers / state

Not applicable — the asset is stateless. No datastore, no session, no cookies, no `localStorage` (deliberately: browser storage is unsupported in the target embed contexts and unnecessary here). All "state" is ephemeral DOM state (which tab is active), reset on reload.

Recorded here only to make the absence explicit and intentional: the system-of-record for this asset's *content* is the GitLab repo (`index.html` under version control); the running Railway instance holds no authoritative state and is disposable.

---

## 7. Freshness / state machine

No runtime freshness logic. The relevant freshness concern is **editorial, not mechanical** — the content references live GTT facts (figures, case studies, service URLs) that can drift.

```
age = now - last_content_review
┌───────────────────────────────────────────────────────────────┐
│  age < 1 quarter     CURRENT    → use as-is                    │
│  1q ≤ age < 2q       AGING      → spot-check gtt.net links/figs │
│  age ≥ 2 quarters    STALE      → re-verify sourced (green)    │
│                                    items before external use   │
└───────────────────────────────────────────────────────────────┘
```

Thresholds fit the use case: GTT case-study figures and service pages change on roughly quarterly-to-annual cadence, and the purple "Solution Architecture" content is analytical (doesn't expire the same way). A quarterly spot-check on the green (sourced) items is proportionate; the sourced-vs-original split in the Sources block tells a reviewer exactly which rows need the check.

---

## 8. Deployment topology

```
 Railway project: gtt-hpe-use-cases
 ────────────────────────────────────────────────
   service (web)
     builder:  NIXPACKS
     build:    npm install         → pulls serve@14.2.1
     start:    npx serve -s . -l $PORT
     image:    node (Nixpacks default)
     port:     $PORT (Railway-injected)
     volumes:  none
     domain:   <generated>.up.railway.app   (Settings → Networking)

 Alt: Microsoft Teams
     channel tab (type: Website) ──iframe──▶ Railway domain
```

Why every storage decision is **replicas:1 / none**

| Store | Why locked | Recovery |
|---|---|---|
| none (stateless) | Nothing to persist; content lives in Git, runtime is disposable | Redeploy from repo; zero data-loss risk by construction |

Migration path: if the asset ever needs auth-gating (GTT-internal only) or usage analytics, that's the trigger to move off bare static-serve — but neither is a current requirement, and adding them now would be premature. Revisit if it goes customer-facing at scale (§14).

---

## 9. Failure modes — graceful degradation

| Failure | Behavior |
|---|---|
| Google Fonts CDN blocked/unreachable | Page renders in system-font fallback; layout and content fully intact, only typography changes |
| Tab JS fails / disabled | Print CSS path reveals both use-case panels; asset becomes a full linear document — no content lost |
| gtt.net link dead / moved | Individual link 404s in a new tab; rest of tool unaffected (links are referential, not embedded) |
| Railway instance down | Asset unreachable until redeploy; zero data loss (stateless, Git-backed); local `index.html` double-click still works for the holder |
| Teams iframe blocked by tenant policy | Falls back to opening the Railway URL directly in a browser tab |

Core principle: the **only** failure that breaks the core purpose is Railway being down *and* no local copy — and even that is a pure availability blip with zero data consequence, recoverable by redeploy or by opening the file locally. Everything else degrades quality (fonts, a dead link, no tab animation), never the message.

---

## 10. Phased rollout

| Phase | Adds | Validation |
|---|---|---|
| P1. Local review | `index.html` opened locally; internal read-through | Stakeholders confirm content + funnel order; no deploy needed |
| P2. Railway staging | Repo → Railway → generated domain | URL renders identically to local; tabs, links, fonts work on a public host |
| P3. Claims sign-off | Claims owner reviews purple (Solution Architecture) items in Sources block | Sign-off recorded before any external/customer exposure |
| P4. Teams distribution | Website tab in the relevant GTT channel(s) | Renders inline in Teams; reps can reach it in-flow |
| P5. Field feedback loop | Optional: light usage signal / rep feedback intake | Iterate content on the §7 freshness cadence |

Each phase is independently revertable: P2–P4 are config/hosting toggles (delete the Railway domain, remove the Teams tab) with no content risk; P1/P3 are review gates, not builds. Nothing in a later phase mutates an earlier one.

---

## 11. Config / new env vars

```
PORT        (Railway-injected)   port the static server binds; no manual default needed in prod
```

Dependency additions (`package.json`):

```
serve   14.2.1   (static file server; only runtime dependency)
```

No application config, no secrets, no `.env` required. `railway.json` pins the start command (`npx serve -s . -l $PORT`) and restart policy (ON_FAILURE, max 3).

---

## 12. Files that will change

```
gtt-hpe-deploy/            # deploy package (new)
├── index.html             # the tool (renamed from gtt-hpe-use-cases.html)
├── package.json           # declares serve dep + start script          (new)
├── railway.json           # pins start command + restart policy        (new)
├── .gitignore             # node_modules/, .DS_Store, *.log            (new)
└── README.md              # non-coder deploy steps (GitHub/Railway/Teams) (new)

docs/
└── gtt-hpe-use-cases-tech-arch.md   # this document                    (new)
```

No existing repo files are modified; this is an additive deploy package plus its doc.

---

## 13. Open decisions

Resolved (kept for the record)

- **Engagement builder, not a pricing tool.** ✅ Decided 2026-07-01 — no hard dollar figures anywhere; stakes are provocation questions the prospect answers themselves. Rationale: printed numbers invite argument and premature price anxiety; a customer's own number persuades harder. See §2, §5.
- **Problem before technology.** ✅ Decided 2026-07-01 — outcomes ("The shift") moved *ahead* of the reference architecture in each use case; "put in front of the CFO" framing removed. Rationale: result should precede mechanism. See §3, §5.
- **Industry archetypes, not named accounts.** ✅ Decided 2026-07-01 — scenarios softened to "the typical multi-site QSR brand" / "the typical multinational manufacturer." Rationale: reusability across every account in the vertical; avoids reading as one enterprise. See §2.
- **HPE-only partnership framing.** ✅ Decided 2026-07-01 — no Fortinet/Palo Alto mention despite GTT's multi-vendor SASE; this is an HPE-only asset. Rationale: per project owner. See §4 row 5 (out of scope here).
- **Static serve on Railway vs. GitLab Pages.** ✅ Decided 2026-07-01 — Railway, to match existing team deploy muscle and give a Teams-embeddable URL. Rationale: consistency with prior React/Railway workflow; one path to both review link and Teams tab. See §8.
- **Provenance surfaced in-tool.** ✅ Decided 2026-07-01 — Sources block splits green (sourced) vs purple (Solution Architecture). Rationale: makes the asset publishable-with-review rather than re-review-everything. See §2, §7.

Still open

- **Self-hosted fonts vs. CDN.** CDN keeps the package tiny and needs no build; self-hosting the two font families makes it bulletproof behind a corporate firewall that blocks Google Fonts. Tips to self-host if Teams-behind-firewall rendering shows fallback fonts in P4 validation. See §9.
- **Auth-gating (GTT-internal only).** Currently open on a public Railway URL. If the asset must not be reachable outside GTT, that forces a move off bare static-serve (§8 migration path). Tips to gated if it's ever deemed sensitive/internal-only; stays open if a semi-public review URL is acceptable.
- **Usage analytics.** No signal today on whether reps actually use it. A lightweight privacy-respecting counter would inform the §7 iteration cadence; tips to adding one if P5 feedback is too thin to act on. Weighed against keeping the asset dependency-free.

---

## 14. Out of scope

- **The GTT+HPE network/security solution architecture itself** — SD-WAN/SSE/ZTNA topology, PoP/backbone design, policy engine, device-intelligence internals. This doc covers the *asset that sells it*; the solution gets its own overview doc. Revisit when that overview is written (cross-ref target).
- **PowerPoint / PDF variants** — a deck and a one-page leave-behind were floated; deferred until the HTML asset is validated (P1–P3). Revisit post-sign-off.
- **Fit-scoring engine** — the ICPs are firmographic profiles, not a weighted scoring model. A scoring workbook is a separate asset; revisit if reps need account-ranking, not just qualification.
- **CMS / content-editing UI** — content is edited in-repo as HTML. No non-technical editing surface. Revisit only if edit frequency outgrows the Git workflow.

---

## Cross-references

| For details on… | Read |
|---|---|
| The GTT+HPE solution being sold (SD-WAN/SSE/ZTNA/backbone) | `gtt-hpe-solution-overview-tech-arch.md` *(to be written)* |
| House doc conventions | `_TEMPLATE-tech-arch.md` |
| Non-coder deploy steps (GitHub/Railway/Teams) | `README.md` (in deploy package) |
| Content provenance (sourced vs. original) | Sources & provenance block, in `index.html` footer |
