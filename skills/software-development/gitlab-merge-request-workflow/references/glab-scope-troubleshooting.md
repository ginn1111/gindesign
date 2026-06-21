# glab MR update — 403 insufficient_scope

Use when `glab mr update` fails even though `glab auth status` says logged in.

## Symptom

```text
glab mr update <iid> --description "$(cat body.md)" --yes
```

returns:

```text
403 insufficient_scope
The request requires higher privileges than provided by the access token.
```

## Check

```bash
glab auth status
glab api /personal_access_tokens/self
```

Example token payload that still fails:

```json
{
  "scopes": ["read_user", "read_repository", "read_api", "write_repository"]
}
```

## Lesson

Repository write scope alone does not guarantee MR metadata write access. For MR description/title updates, token may need `api` scope.

## Retry pattern

1. Save desired MR body to file.
2. Inspect token scopes with `/personal_access_tokens/self`.
3. Re-auth with token that includes `api`.
4. Re-run exact `glab mr update` command.
5. Re-fetch MR with `glab mr view <iid> -F json`.

## Extra pitfall

On some `glab` versions:
- `glab mr view --json` fails
- `glab mr view -F json` works

## Multi-MR branch pitfall

If current branch matches more than one MR, bare `glab mr view` can fail with candidate list. Use explicit IID like `glab mr view 30` and `glab mr update 30 ...`.
