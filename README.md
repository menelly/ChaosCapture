# 🐙 Chaos Capture

**A local-first read-later app.** Paste article & newsletter links → bundle a batch into a clean **EPUB** → sideload to your e-reader. Phone captures, desktop compiles, the queue syncs over your **LAN** (QR-paired, same-WiFi, **zero cloud**).

Part of the **Chaos family** — Command · Compass · Clean-Up · **Capture**.

## Status
🌱 **Scaffolding.** v1 MVP in progress — see **[BRIEF.md](BRIEF.md)** for the full build plan and the reuse manifest.

## The idea
Kate wanted *"an app I can send articles/newsletters to that bundles a week's worth into an EPUB for my e-reader."* This is that — built **local-first**, so a reading list never touches a cloud.

## v1 flow
1. 📱 **Mobile** — paste/share a link → it lands in a synced queue.
2. 🔗 **Sync** — the queue moves phone↔desktop over **LAN** (QR pair, same WiFi). No server, no cloud.
3. 💻 **Desktop** — *"Compile to EPUB"* → one EPUB (each article a chapter + a TOC) → download → sideload to Kindle.

**Bundling:** accumulate links over the week → compile the whole batch into one EPUB → queue clears.

## Stack
Tauri 2 (Rust) + Next.js 15 (static export) + TypeScript / Tailwind / shadcn — the Chaos stack.

## Architecture: reuse, don't rebuild
Most of this app **already exists** in Command and Compass and is *lifted*, not rebuilt — the Tauri shell, the **LAN QR-sync engine**, the hybrid Dexie/SQLite DB, the shadcn themes, the fonts, the UI kit. The genuinely-new code is just **four modules**: URL fetch, readability extraction, EPUB assembly, and the two screens. Full file-by-file manifest in **[BRIEF.md](BRIEF.md)**.

## Deferred (Phase 2 — do not start yet)
Email / share-target auto-ingest · auto-send-to-Kindle delivery · citation-manager.

---
Built by **Ace** (she/her) with **Ren**, 2026. 🐙💜
