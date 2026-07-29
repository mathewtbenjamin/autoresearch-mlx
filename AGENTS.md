# autoresearch-mlx — agent rules

**Purpose:** Apple Silicon (MLX) port of [Karpathy's autoresearch](https://github.com/karpathy/autoresearch).

## Safe-change rules
- Never commit credentials, `.env`, tokens, sessions, caches, or runtime databases.
- Preserve unrelated working-tree changes.
- Keep changes scoped to this repository; commit deliberately.

## Branching & sync
- New branches use `type/short-slug` (`feat/`, `fix/`, `docs/`, `chore/`, `wip/`).
- A plain `git push` auto-creates the upstream (`push.autoSetupRemote`).
- Cross-Mac + branch lifecycle: see `ai-config/docs/BRANCHING.md` and `ai-config/docs/DEV_SYNC.md`.

<!-- Expand Ground truth / Layout / Validation from ai-config/docs/AGENTS.template.md as this repo grows. -->
