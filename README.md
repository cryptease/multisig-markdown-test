# Compound Multisig — Exported documents

This repository holds **markdown exports** produced by the [compound-multisig-history-backend](https://github.com/woof-software/compound-multisig-history-backend) application. The backend pushes updated files here on a schedule; the content is read-only from the point of view of this repo.

---

## Contents

All exported files live under **`files/`** (the directory is created automatically if it does not exist).

| File | Description |
|------|-------------|
| `files/about.md` | About page content (plain markdown from the app). Ends with `Generated at: <ISO>`. |
| `files/actions.md` | Full list of Safe actions (transactions): one section per action with a markdown table of fields. Ends with `Generated at: <ISO>` and `Total: N`. |
| `files/signers.md` | Full list of signers: one section per signer with a markdown table of fields. Ends with `Generated at: <ISO>` and `Total: N`. |

Each file includes a **generation timestamp** at the end for traceability.

---

## How updates work

- The backend runs a **daily cron job** (1:00 AM) that spawns a separate process. Only one push runs at a time (no overlap).
- That process fetches the three documents from the backend’s ops endpoints (localhost-only), then clones this repo, creates the `files/` directory if needed, writes `files/about.md`, `files/actions.md`, and `files/signers.md`, commits, and pushes.
- The backend must be configured with this repo’s URL and a GitHub token (`MARKDOWN_REPO_URL`, `MARKDOWN_GITHUB_TOKEN`). Default branch used for push is `main` (configurable via `MARKDOWN_BRANCH`).

### Manual trigger (admin)

You can trigger a push manually from the **admin panel**: use the “Markdown push” (or equivalent) action; it calls `POST /v1/admin/ops/markdown-push` (Bearer required). The response is `202 Accepted` with `{ started: true }` if the job was started, or `{ started: false }` if a push is already running. The admin UI can call `GET /v1/admin/ops/markdown-push` to show whether a push is currently running (`{ started: boolean }`).

**Do not edit the files in `files/` manually** if you want them to be overwritten by the next run; the job replaces their contents on each push.

---

## Source

- **Backend:** [compound-multisig-history-backend](https://github.com/woof-software/compound-multisig-history-backend)
- **Export job:** `src/infrastructure/markdown/markdown-push.runner.ts`
- **Manual trigger:** admin panel (`POST /v1/admin/ops/markdown-push` with Bearer), or from the backend repo with app running and env set: `pnpm run job:markdown-push`
