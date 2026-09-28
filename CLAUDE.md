# CLAUDE.md

Working memory for Claude in this repo (`teddiesloco/x402`, a fork of `x402-foundation/x402`). Read this first, then `Goal.md` for intent and goals.

## Memory

This section is the durable memory for the repo. Keep it short, factual and current.

**Where memory lives**

| What | Where |
|---|---|
| Intent, goals, BA/PO/PM view | `Goal.md` |
| Stable facts, conventions, decisions for this repo | this file, "Facts" and "Decisions" below |
| Protocol truth | `specs/` (never restate it here; link) |
| Contribution rules | `CONTRIBUTING.md`, `.agents/skills/contributing/SKILL.md` |
| Docs rules | `docs/AGENTS.md` |
| Cross-session notes in the Claude project | project memory store (one fact per file) |

**What to remember.** Things a new session would otherwise have to ask Teddy again or rediscover: decisions Teddy made, the fork's own goals, conventions that are not written elsewhere, non-obvious gotchas.

**What not to remember.** Anything the code, git history, specs or existing docs already record; one-off task details; secrets, keys, private keys, RPC URLs with tokens.

**How to update.**

1. Change `Goal.md` when a goal changes; change this file when a fact or decision changes.
2. One line per fact, with the date (YYYY-MM-DD) for decisions.
3. Replace or delete a stale line instead of adding a contradicting one.
4. Keep this file under ~200 lines; move detail into the linked source.

### Facts

- Fork is identical to upstream as of 2026-09-28 (no fork-only commits). Upstream remote is not configured; `origin` is the fork.
- TypeScript is the reference SDK; Python and Go must match its behavior. Java and every `legacy/` path (`typescript/packages/legacy/`, `go/legacy`, `python/legacy`) are frozen: security fixes only.
- Protocol v2 only for new work: `PAYMENT-REQUIRED` / `PAYMENT-SIGNATURE` / `PAYMENT-RESPONSE` headers, `amount` field, CAIP-2 networks (`eip155:84532`), atomic units.
- Schemes: `exact`, `upto`, `batch-settlement`, `auth-capture` (`specs/schemes/`). Transports: HTTP, MCP, A2A (`specs/transports-v2/`).
- Write "onchain", never "on-chain".
- Upstream says `CLAUDE.md` is not committed there; do not include this file or `Goal.md` in PRs to `x402-foundation/x402`.

### Decisions

- 2026-09-28: `Goal.md` (Vietnamese) holds intent/goals from BA, PO and PM views; this file holds memory conventions. Fork-specific goal is still open.

## Working with Teddy

- Teddy writes in Vietnamese; reply in Vietnamese. Code, commits and PRs stay in English.
- Work on a branch and open a draft PR; never push to `main`.

## Rules for code (x402 moves real money)

- Read the relevant spec in `specs/` before touching payment, signing or settlement logic. Never invent headers, fields or flows.
- Never hardcode chain IDs, token addresses or decimals from memory; use the constants in `mechanisms/<family>/`.
- Match surrounding code; minimal diff; one issue per PR; no unrequested features or dependencies.
- Bug fix = test that fails before and passes after.
- Changelog fragment for user-facing changes: `pnpm -C typescript changeset`, `make -C go changelog-new`, or Towncrier in `python/x402/changelog.d/`.
- SDK change → check `docs/AGENTS.md` code-to-doc mapping.

## Commands

| SDK | Dir | Format / lint | Test |
|---|---|---|---|
| TypeScript | `typescript/` | `pnpm format && pnpm lint` | `pnpm test` (`pnpm --filter @x402/evm test` for one package) |
| Python | `python/x402/` | `uvx ruff format && uvx ruff check` | `uv run pytest` |
| Go | `go/` | `make fmt && make lint` | `make test` (`make verify` runs all) |
