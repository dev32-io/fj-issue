# fj-issue

Attach files and images to Forgejo issues and pull requests from the command
line, using the login you already have from [`fj`](https://codeberg.org/forgejo-contrib/forgejo-cli).

`fj` (v0.6) can create, view and comment on issues but can't upload
attachments. `fj-issue` adds that, and passes every other command through to
`fj issue`. Drop it once `fj` gains attachment support
([forgejo-cli#709](https://codeberg.org/forgejo-contrib/forgejo-cli/issues/709)).

## Usage

```sh
# Upload to issue/PR #12 and print markdown for each file
fj-issue attach 12 shot.png build.log

# Upload, then post a comment embedding the files
fj-issue attach 12 -m "Before and after:" before.png after.png

# Upload to an existing comment
fj-issue attach -c 3456 trace.txt

# List / download attachments on the issue and all its comments
fj-issue attachments 12
fj-issue download 12 ./issue-12

# Anything else goes to `fj issue`
fj-issue view 12
fj-issue create "Bug" --body "..."
```

Options for `attach`, `attachments` and `download`:

| Option | Meaning |
| --- | --- |
| `-r, --repo owner/repo` | Repository. Default: parsed from the git remote. |
| `-H, --host host` | Forgejo host. Default: the git remote's host if `fj` is logged in there, otherwise the only host `fj` is logged in to. |
| `-R, --remote name` | Git remote used for detection. Default: `origin`. |

## Auth

It reads the token `fj auth login` saved:

- macOS: `~/Library/Application Support/forgejo-cli.forgejo-cli/keys.json`
- Linux: `${XDG_DATA_HOME:-~/.local/share}/forgejo-cli/keys.json`

Override the file with `FJ_KEYS_FILE`, or skip it with `FJ_ISSUE_TOKEN`.
The token needs the `write:issue` scope to upload. That file layout is
internal to `fj`, so a future `fj` release could break detection.

## Notes

- Files are all checked before any upload, but uploads aren't rolled back:
  if one fails mid-batch, earlier files stay attached.
- Accepted file types and sizes are set by the server
  (`GET /api/v1/settings/attachment`); a rejection there is not a client bug.
- HTTPS only.

## Install

Requires bash 3.2+, `curl`, `jq`, `git`.

```sh
ln -s "$PWD/fj-issue" ~/.local/bin/fj-issue
```
