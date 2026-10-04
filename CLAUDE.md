@AGENTS.md

# VideyoHT

A rebranded, security-hardened fork of wide-trace/open-higgsfield: a Next.js 16 studio for Higgsfield image/video generation (including `bytedance/seedance-2.5/*`). See README.md for features and architecture.

## Commands

- `pnpm install`: install dependencies (pnpm 12; `pnpm-workspace.yaml` holds build-script allowlist and `overrides`)
- `pnpm dev`: dev server bound to `127.0.0.1:3000`. Keep the `-H 127.0.0.1`; the studio must not be reachable from the LAN or from phones.
- Don't add LAN/phone access (`-H 0.0.0.0`, `allowedDevOrigins`). The user wants the studio reachable from this PC only.
- `npx tsc --noEmit`: typecheck (no test suite in this repo)
- `pnpm audit`: must report no known vulnerabilities after any dependency change

## Environment

- `.env.local` (git-ignored) needs `HF_API_BASE_URL=https://api.higgsfield.ai`. Without it, generation throws `Missing HF_API_BASE_URL`.
- `OPEN_HIGGSFIELD_READ_WRITE_TOKEN` is intentionally unset. `/api/blob` has no auth, so don't set it without adding auth first.
- Never read, print, log or commit `.env.local` or the user's platform key.

## Security invariants

- The platform key lives only in the `httpOnly` `api_key` cookie (`src/generation/credentials.ts`) and is sent only to `HF_API_BASE_URL` (`src/generation/platform.ts`). Don't add any other destination, and don't log request headers.
- Generation goes through server actions (`src/generation/actions.ts`), which are origin-checked by Next.js. Don't move it to an unauthenticated API route.
- `undici` is overridden to `^6.28.1` in `pnpm-workspace.yaml`; keep it until `@vercel/blob` ships a patched range.

## Branding

- Name: `SITE_NAME = "VideyoHT"` in `src/site.ts` (drives `<title>`, manifest, metadata).
- Logo: `.ohf-brand` in `src/openhiggsfield/topbar.tsx` with styles in `src/openhiggsfield/openhiggsfield.css`. It's pinned top-left, the wordmark is hidden ≤900px and the whole logo ≤560px.
- Brand color is the existing `--accent` (`#d1fe17`); use the CSS variable, not a new hex.
- Favicon: `src/app/icon.svg`. Not yet rebranded: `src/app/apple-icon.png` and `public/og.png` (regenerate via `pnpm brand` / `scripts/build-brand-assets.mjs`).

## Git

- Work on the `videyoht` branch. `main` mirrors upstream.
- `next dev` regenerates `AGENTS.md` and touches `next-env.d.ts`. Don't hand-edit AGENTS.md's managed block.
