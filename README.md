# bug-hunt

Headless, repo-agnostic bug hunt: finders read the hottest files from five angles, provers write a repro that must fail against the current code, and a verifier must turn it green with a fix before a bug is reported. Report only; `bug-hunt run` wraps `claude -p` in a throwaway worktree with a spend cap and exits 1 on proven bugs.

A finding is only a finding once a failing repro and a root-cause fix prove it. The tool never fixes in place: it reports Markdown and JSON, and exits 1 when proven bugs exist so CI can gate on it.

## Story

Approved 2026-09-26: *"bug-hunt is approved for addition"* (Zach, 18:06), after the plan (headless, repo-agnostic; a finding needs a repro that fails against the current code; Markdown and JSON report, exit 1 on findings; report only; git worktree at HEAD; hot files, $10 cap) was approved in plan mode.

The verbatim ledger row from [`SETUP-LIST.md`](https://github.com/locriani/ai-additions/blob/main/SETUP-LIST.md) in [locriani/ai-additions](https://github.com/locriani/ai-additions):

```
| `bug-hunt` | 2026-09-26 | "bug-hunt is approved for addition" (Zach, 18:06), after the plan (headless, repo-agnostic; a finding needs a repro that fails against the current code; Markdown and JSON report, exit 1 on findings; report only; git worktree at HEAD; hot files, $10 cap) was approved in plan mode | **Yes** — 2026-09-26 19:18 CT, on "commit, merge, install, approved for enablement" (19:17) after PR 18 merged (28dc63d): `claude plugin marketplace update ai-additions` + `claude plugin install bug-hunt@ai-additions --scope user` (0.1.0). 22 tests OK from the installed copy; live run on the seeded fixture exit 1 with only the planted bug; updated to **0.1.1** on 2026-09-26 at 19:3x CT on "do it" (after the security review flagged unsandboxed Bash) via PR 19 (aa28d90): Bash runs in a fail-closed sandbox (writes only the worktree, its git dir and the out dir; no credentials; registries-only network, `--allow-domain` for more). 24 tests OK; live seeded hunt exit 1, 3 of 4 candidates refuted |
```

## Provenance

Extracted 2026-10-10 from [locriani/ai-additions](https://github.com/locriani/ai-additions) at commit `a01d7eb` (plugin history: `git log` in that repo, path `plugins/bug-hunt` — 2 commits, both 2026-09-26, carried intact with their original authorship and messages). Content is carried verbatim: bug-hunt had no cross-plugin or umbrella references that break at a repo root.

## Install

```bash
claude plugin marketplace add locriani/bug-hunt
claude plugin install bug-hunt@bug-hunt
```

Update: `claude plugin marketplace update bug-hunt`, then reinstall.

## Layout

- `bin/bug-hunt` — the CLI. `bug-hunt run` wraps `claude -p` in a detached worktree at HEAD with no permission prompts and a spend cap; `bug-hunt report` validates `findings.json`, renders `findings.md`, and exits 0 clean, 1 on proven bugs, 2 otherwise.
- `skills/bug-hunt/` — `SKILL.md` and the five finder-angle prompts (`prompts.md`).
- `tests/` — stdlib `unittest` suite plus the seeded fixture: a planted bug whose repro fails against the seeded code by design.
- `.claude-plugin/` — `plugin.json` and this repo's own `marketplace.json`.

## Development / Tests

Requires `python3` (stdlib only) and `git`. The unit suite fakes the `claude` CLI — a real hunt needs `claude` on PATH; the tests do not.

```bash
python3 -m unittest discover -s tests
```

CI (`.github/workflows/tests.yml`) runs the same command on every push, in a git-capable `python:3.14` container, under the house posture: push-only trigger, `contents: read` token, SHA-pinned checkout with no persisted credentials.

## License

MIT — see [LICENSE](LICENSE).
