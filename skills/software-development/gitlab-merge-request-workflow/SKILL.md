---
name: gitlab-merge-request-workflow
description: "GitLab merge request workflow with glab: inspect, update, verify, and troubleshoot auth scopes."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos]
metadata:
  hermes:
    tags: [GitLab, glab, Merge-Requests, Git, Authentication]
---

# GitLab Merge Request Workflow

Use when user asks to create, update, inspect, or manage a GitLab merge request with `glab`.

## Core rule

Do action with real `glab` output. Do not assume current branch maps to one MR. Resolve exact MR first, then update, then verify.

## 1. Resolve target MR

Current branch can have multiple matching MRs. Do not run bare `glab mr view` and guess.

Preferred flow:

```bash
git branch --show-current
glab mr view <iid> -F json
```

If branch has multiple MRs, `glab mr view` may return a chooser-style error listing candidates. Use listed IID explicitly.

Example failure pattern:

```text
You must select a merge request: merge request ID number required. Possible matches:
!30 ...
!28 ...
```

Rule: pick exact `!iid`, then continue with `glab mr view <iid>` or `glab mr update <iid>`.

## 2. Inspect MR before editing

Get current title/body/base/head first:

```bash
glab mr view <iid> -F json
```

If you only need human-readable output:

```bash
glab mr view <iid>
```

Note: some `glab` versions use `-F json`, not `--json`.

## 3. Update description safely

For multi-line bodies, write markdown to a file first, then pass it to `glab`.

```bash
glab mr update <iid> --description "$(cat path/to/body.md)" --yes
```

Why: avoids shell quoting mistakes and keeps reusable body text on disk for retry.

## 4. Verify update

After update, fetch MR again and confirm body changed:

```bash
glab mr view <iid> -F json
```

Do not claim success until refreshed output shows new description.

## 5. Auth troubleshooting for `glab mr update`

If update fails with 403 `insufficient_scope`, inspect token scopes directly:

```bash
glab auth status
glab api /personal_access_tokens/self
```

Look at `scopes` in response.

### Important pitfall

`write_repository` is not enough for merge-request metadata edits.

If token only has scopes like:
- `read_user`
- `read_repository`
- `read_api`
- `write_repository`

then `glab mr update` can still fail with:

```text
403 insufficient_scope
The request requires higher privileges than provided by the access token.
```

Fix: use token with `api` scope, then re-auth and retry.

## 6. Re-auth flow

```bash
glab auth status
glab auth login
```

If using env var auth, set token with needed scopes before retry.

## 7. Good operator pattern

1. Resolve exact MR IID.
2. Inspect current MR.
3. Write body to markdown file.
4. Run `glab mr update <iid> --description "$(cat file)" --yes`.
5. Re-fetch MR and verify body.
6. If 403, inspect token scopes with `glab api /personal_access_tokens/self`.
7. Ask user for new token or re-auth instructions only after proving scope mismatch.

## Pitfalls

- Bare `glab mr view` can fail when multiple MRs share same source branch.
- `--json` may be unsupported; use `-F json` on some versions.
- Do not stop at `glab auth status`; it shows login presence, not enough scope. Use `/personal_access_tokens/self` to inspect real scopes.
- Keep MR body in `*.md` file so retry after auth fix is one command.

## Support files

- `references/glab-scope-troubleshooting.md` — concrete 403 insufficient_scope diagnosis and retry pattern.
