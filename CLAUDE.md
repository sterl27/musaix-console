# Musaix (MusaixConsole)

Canonical production shell for Musaix Pro — a local-first studio command surface, not a throwaway mock. Future modules plug into it: Cassette Cockpit, AI Visualizer, multi-agent orchestration, Web Audio engine, session state, channel adapters.

## Repo layout
- `apps/web` — **MusaixConsole**: the Next.js studio shell itself (three-panel layout, nav rail, workspace launchpad, performance panel)
- `apps/source-atlas` — **Source Atlas Gameboard**: every source in a session as a node you can move/link/inspect/queue work against. Next.js shell + Phaser 2D canvas (the `game/` dir), backed by a Cloudflare Durable Object when available. Runs local-only with no Worker configured.
- `apps/atlas-worker` — Cloudflare Worker, the "spine" for Source Atlas: session routing, asset custody, job dispatch, memory lookup. Coordination only — no MIR (music info retrieval) work runs here; `ANALYSIS_QUEUE` hands off to a Python analysis tier that reports back.
- `packages/atlas-core` (`@workspace/atlas-core`) — shared fixtures/types consumed by source-atlas and atlas-worker
- `packages/ui`, `packages/eslint-config`, `packages/typescript-config` — shared workspace packages
- `docs/adr/` — architecture decision records; check here before proposing an architecture change
- `docs/huggingface-music-ingestion.md` — plan for ingesting HF music datasets into a local research corpus (JSONL), feeding a future Supabase metadata table + vector index

## Stack
pnpm 10.33.4 workspace + Turborepo 2.8.17, Node >=20, Next.js 16.1.6, React 19.2.4, Tailwind/PostCSS v4, TypeScript 5.9.3. Assumes shadcn/ui-compatible primitives via `@workspace/ui`.

## Commands
- `pnpm dev` / `pnpm build` / `pnpm lint` / `pnpm typecheck` / `pnpm format` — fan out across the whole workspace via Turbo
- `pnpm --filter web dev|build|start` — MusaixConsole only (default port)
- `pnpm --filter source-atlas dev` — http://localhost:3001
- `pnpm --filter atlas-worker dev` — http://127.0.0.1:8787 (no Cloudflare account/`wrangler login` needed for local dev; Durable Object runs locally, other tiers stubbed)

## Design language
"Obsidian Precision" (deep void base, tonal layering, low-contrast borders, disciplined spacing) for structure + "Liquid Noir" (glassmorphism, glow accents, scanline texture, reactive neon on active controls) for atmosphere. This is a focused studio instrument, not a generic admin dashboard — avoid generic dashboard UI patterns.

## Current state
MusaixConsole is in shell-first foundation mode. Built: the console shell, drawer shells for Cassette Cockpit/AI Visualizer, `useMusaixAudioEngine`/`useMusaixAgents` stubs, Obsidian Precision tokens. **Not built yet**: real Web Audio transport, AudioWorklet pipeline, real metering/spectrum analysis, recording/export, DAW bridge, real multi-agent runtime, Teams adapter, OpenClaw/eve hybrid architecture, persistent session storage. Check the root README's "Scope" section before assuming an audio feature works end-to-end — most are stubs.
