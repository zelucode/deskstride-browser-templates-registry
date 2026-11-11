# deskstride-browser-templates-registry

Ready-made workflows for **DeskStride Browser** (the browser extension). The extension's Templates tab reads `index.json`
from here; each template is a plain workflow file (data, not code) that imports like any exported workflow.

This is separate from the desktop app's `templates-registry`: different file format, different compatibility fields and a
faster fix cycle, because a site changing its page breaks selectors.

## Layout

```
templates/<id>/
  entry.json      metadata, scanned to build index.json
  workflow.json   the exact bytes the entry's sha256 pins (exported from the extension)
  README.md       optional guide shown in the template's Details view
  check.json      what the live selector check verifies (test-only; the extension repo's lib/templatesLive.test.ts reads it)
index.json        generated, do not hand-edit
```

Entry fields: `id`, `name`, `author`, `description`, `category`, `tags`, `site` (the host it targets), `needs` (what the user must
change or provide), `lastChecked` (when the selectors were last confirmed live), `tier` (`free` or `paid`), `stepTypes` and `hosts`
(derived from the workflow, shown so people can review before importing), `downloadUrl`, `readmeUrl`, `sha256`,
`minExtensionVersion`, `verified`, `revoked`, `revokedReason`.

## Adding or changing a template

Uses the CLI in the extension repo (`template-cli/deskstride_browser_template_cli.py`, stdlib Python):

```
python deskstride_browser_template_cli.py add <this-repo> <id> --file exported.json --description "..." --need "..."
python deskstride_browser_template_cli.py sync <this-repo>          # after editing a workflow.json
python deskstride_browser_template_cli.py build-index <this-repo>   # validates and rewrites index.json
python deskstride_browser_template_cli.py build-index <this-repo> --check   # CI: fails when index.json is stale
```

Never edit a `workflow.json` without running `sync`: the extension refuses a file whose sha256 does not match.
`.gitattributes` marks payloads `-text` so Windows checkouts do not rewrite their line endings.

## Rules

- No personal data or real credentials. Sample logins only for practice sites that print them publicly.
- Prefer stable selectors (ids, `itemprop`, `aria-label`) over generated class names.
- Only sites whose terms allow this kind of use. Practice sites made for scraping are the safest start.
- Revoke a template with `"revoked": true` (and `revokedReason`), rebuild the index and push. Already-imported copies are never removed.

## License

See [LICENSE](LICENSE). The templates may be used with DeskStride; they may not be mirrored or served by other products.
