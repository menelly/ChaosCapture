# 🐙 Chaos Capture — Swarm Brief (v1 / freeze-safe MVP)

**Written 2026-07-17 ~4am by Ace, with Ren. For the EAP swarm — Projects read-only freeze is Tue 7/21 8pm ET, so every task must reach a *self-contained, mergeable* state by then.**

## What it is (one line)
A **local-first read-later app**: paste article/newsletter links → bundle a batch into a valid **EPUB** → sideload to your e-reader. **Phone captures, desktop compiles, queue syncs over LAN.** New member of the Chaos family (Command / Compass / Clean-Up).

## The v1 flow (what "done" looks like)
1. **📱 Mobile = capture.** Paste/share a link → it lands in a persistent **queue**.
2. **🔗 Sync.** The queue syncs phone↔desktop over **LAN (QR-paired, same-WiFi, zero cloud)** — lifted from Command.
3. **💻 Desktop = compile.** See the synced queue → **"Compile to EPUB"** → *one* EPUB (each article a chapter, with a TOC + metadata) → **download** → user manually sideloads to Kindle.

**Bundling model:** accumulate-then-batch. Links pile into the queue over the week; "Compile to EPUB" packages *all pending* into one file; queue clears.

## 🔑 THE GOLDEN RULE: REUSE, DON'T REBUILD
This is why it's freeze-safe. Most of the app already exists in Command/Compass. **Copy-lift the pieces below into the new repo** (do NOT extract a shared library — that's a post-freeze refactor). Named, file-level:

| Piece | Lift from | Files / notes |
|---|---|---|
| **App shell** (Tauri 2 mobile+desktop) | Command | `command-mobile2` scaffold + `tauri.conf.json` |
| **LAN QR-sync engine** | Command | `src-tauri/src/server.rs` (TcpListener 0.0.0.0), `src-tauri/src/sync.rs` (UDP discovery), `peer_store`, `lib/auto-sync.ts`, `app/sync/page.tsx` (QR pair/scan) |
| **Local DB** | Command | hybrid Dexie(web)/SQLite(native) router: `lib/database/hybrid-router.ts`, `dexie-db.ts`, `sqlite-db.ts` |
| **Themes** (shadcn token system) | Command/Compass | `body.theme-X` vars, the 15 themes |
| **Fonts** (built-in stack incl. Atkinson) | Compass | the `StampFont` / `builtin:atkinson` handling |
| **UI kit** (shadcn primitives: modals, cards, buttons) | both | |
| **File-save / download flow** | Compass | the PDF-export save pattern → repoint at EPUB bytes |

## 🆕 THE ONLY GENUINELY-NEW CODE — 4 modules (parallelizable)

### M1 — URL input + persistent queue
- Goal: add a link, see pending items, remove one, mark compiled.
- Reuse: the Command DB (hybrid router) for storage; shadcn UI kit for the list.
- Done when: links persist locally, survive restart, and sync between paired devices via the reused LAN sync.

### M2 — Local fetch + readability extraction
- Goal: given a URL, fetch the page **locally** (Tauri Rust — no CORS wall, no backend) and extract clean readable article HTML (strip nav/ads).
- Libraries (swarm confirm latest maintained): fetch = `reqwest` (real User-Agent, follow redirects); extraction = a Rust Readability port (`dom_smoothie` or `readability`), or run Mozilla Readability.js in a hidden webview as fallback.
- Done when: a normal news/blog/newsletter URL → title + author + clean body HTML. Graceful failure (paywalls/JS-walls): keep what's fetchable, flag the item, don't crash the batch.

### M3 — EPUB assembly
- Goal: N extracted articles → one **valid EPUB** (opens on Kindle/Kobo/generic).
- Library: `epub-builder` (Rust) — chapters per article, a generated **table of contents**, book metadata (title = e.g. "Chaos Capture — {date range}", per-chapter title/author/source URL).
- Done when: the EPUB validates and opens on a real e-reader with each article a navigable chapter.

### M4 — The two screens
- Mobile **capture** screen (paste/share → queue) + desktop **compile** screen (queue → Compile → download).
- Reuse: themes, fonts, UI kit. Keep it dead simple.
- Done when: the full v1 flow works end-to-end on desktop; mobile capture + sync if time allows.

## 🚧 Guardrails
- **Self-contained by Tuesday.** Prioritize the **desktop core** (M1 storage + M2 + M3 + desktop half of M4) — that alone is a shippable app (paste on desktop → EPUB). Mobile capture + LAN sync is the *next* ring; wire it if the core lands early.
- **Zero cloud infra.** LAN-only sync, local fetch, local EPUB gen, manual sideload. No backend, no hosted email, no delivery service.
- **Copy-lift, don't refactor Command/Compass.** Don't touch the source apps; copy pieces in.
- **Defer to Phase 2** (do NOT start): email/share-target auto-ingest, auto-send-to-Kindle delivery, and the **citation-manager** bonus.

## Repo & stack
- New repo: **`menelly/chaos-capture`**. Tauri 2 + Next.js (static export) + TypeScript/Tailwind/shadcn — the Chaos stack, so the lifted pieces drop in.

## Definition of Done (v1, the must-hit)
> On **desktop**: paste a link → it's in the queue → hit **Compile** → get a valid **EPUB** that opens on a Kindle with each article as a chapter and a working TOC.

Everything past that (mobile capture, LAN sync, then Phase 2) is upside.

— Ace 🐙, for Ren, 2026-07-17
