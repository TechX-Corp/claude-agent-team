---
name: claude-design-handoff
description: Implement a real UI from a Claude Design handoff. Trigger when someone drops a claude.ai/design/p/<uuid> link, an api.anthropic.com/v1/design/h/<id> share link, or an exported "IDE - <name>.html" prototype, or says "implement this design". Covers fetching both link types, reading order, scope fencing, and render-gate verification.
---

# Claude Design → working UI

## 1. Get the design

**Project link** — `https://claude.ai/design/p/<uuid>?file=Landing.dc.html`
Use the **DesignSync** tool (load it first: ToolSearch `select:DesignSync`).
The uuid in the URL *is* the projectId.

```
list_files(projectId=<uuid>)        # ALWAYS first
get_file(projectId, path)
```

**`list_files` first, every time — even when they name one file.** The `?file=` param
records what they had open, not what exists. A project that looks like one landing page
is routinely a dozen screens; building the named one and stopping ships a product where
every other link drops the user back into the old UI.

`get_file` 404 "project not found" = the session's design scopes expired, not a bad uuid.
Ask them to run `/design-login`. Do not go hunting for a different id.

**Share link** — `https://api.anthropic.com/v1/design/h/<id>` returns a **gzip tarball**,
not a page:

```bash
curl -sL "<url>" -o bundle.tgz && tar xzf bundle.tgz
```

Share links **expire**, and a freshly re-exported one can still 404 for `curl` — the API
does not serve anonymous clients. Recovery: ask the person to open the link in their own
signed-in browser, which downloads the bundle, and hand you the local path. That download
is often a **zip**, not a tarball: `unzip -oq <file> -d .design-cache/`.

Extract into a durable, gitignored path in the repo (`.design-cache/`), never `/tmp`, and
write a screen inventory to `docs/` on first fetch. You may not be able to pull it again.

## 2. Read before you build
1. `README.md` in the bundle — intent
2. `chats/chat*.md` — *why* things look the way they do; this is where the reasoning lives
3. the screens themselves

Then write the screen inventory into `docs/ux-wireframes.md`: every screen, and for each,
which one you are building this pass. **State the denominator** — "3 of 12 screens ported"
— because a partial port that omits its own count reads as complete.

## 3. `.dc.html` runtime → vanilla
Design pages run on the DC runtime (`support.js`): `<x-dc>` templates, `{{ expr }}` bindings,
`<sc-for>` loops, a `<script type="text/x-dc">` React `DCLogic` block.

**Port to plain HTML/CSS/JS. Never ship `support.js` or React into the product.**

| DC | Port to |
|---|---|
| `<sc-for>` | JS-built DOM, or static markup |
| `{{ vals }}` | elements your own code updates |
| `renderVals()` React snippets | static markup + CSS keyframes |
| `componentDidMount` listeners | near-verbatim, minus setState |
| `setInterval(setup, 700)` | run setup once — that loop only existed for runtime re-renders |

`get_file` caps at **256 KiB and truncates silently** (`truncated: true` in the response).
Text pages fit; **PNGs usually do not** — you get a valid header and a corrupt image. Check
the flag before trusting any decoded asset. Replace bitmap backdrops with CSS gradients
(designs normally layer gradients over them anyway) and note the substitution.

## 4. The design may draw a mechanism the product does not have
An email+password form when auth is a hosted-IdP redirect. Counters for data nothing
computes yet. Filter chips for statuses the backend never sets.

Porting those verbatim ships a lie about how the system works — worst case, a password box
that silently discards the password. **Keep the composition, swap the control, and comment
the departure in the file header.** Then raise it: the mismatch is a product decision, not
yours to absorb quietly.

## 5. Render gate
Static review does not catch layout. Serve the page over `localhost`, open it, screenshot
it, compare against the design, fix the differences. Do this before you report done —
"the tests pass" says nothing about whether the screen is right.

Under a strict CSP, anything **inline** (a `style=` attribute, `<style>`, `<script>`, a
webfont `@import`) is silently refused. Hoist into files; never loosen the policy to make
a design work.
