# Remote Falcon Issue Tracker

The single public entry point for **bugs and feature requests against the Remote Falcon platform**, plus a working **External API integration sample** for partners.

If you're a show owner, an FPP plugin user, or a developer integrating with Remote Falcon — this is the right place to start.

---

## Why this repo exists

Remote Falcon is split across [many repositories](https://github.com/Remote-Falcon) — the UI, several backend services, the FPP plugin, viewer-page scripts, page templates, the mobile app, and more. End users shouldn't have to know which repo a bug "belongs in" before filing it.

This repo solves that:

1. **One issue tracker for the whole platform.** File any bug or feature request here, regardless of which service or component it affects. We triage and route from here.
2. **A reference integration for the External API.** A small, runnable example (`external-api-sample/`) showing partners how to authenticate with a JWT and call the External API. Easier to copy than to read.

The repo deliberately does not host code, deployment, or documentation — those live in their respective service repos. This is a contact surface, not a service.

---

## Filing an issue

Go to the [Issues tab](../../issues/new/choose) and pick a template:

| Template | Use when |
|---|---|
| **Bug report** | Something is broken, returns the wrong result, crashes, or doesn't work as documented |
| **Feature request** | You have an idea for a new feature, an enhancement to an existing one, or a UX improvement |

### What to include

The templates ask for the essentials. The single most useful thing you can add is **your show name** — without it, we usually can't reproduce or correlate logs.

For bugs, also include:
- **Where you saw it** — control panel, viewer page (with subdomain), FPP plugin, mobile app, or external API
- **Steps to reproduce** — even rough ones; "click X, then Y" beats "it broke"
- **Expected vs actual behavior**
- **Screenshots or logs** if you have them
- **Browser / OS / device** for UI bugs; **FPP version** for plugin bugs

### Where *not* to file

A few things are better filed elsewhere:

- **General questions / "how do I do X?"** — try the [Remote Falcon docs](https://docs.remotefalcon.com) or the community Discord first. Issues are for things that need a code or doc change.
- **Account / billing issues** — these are private and should go through email, not a public issue.
- **Security vulnerabilities** — please **do not** open a public issue. Report privately by emailing the maintainer (see the org page for current contact).

---

## External API sample

`external-api-sample/` is a working example of authenticating against the Remote Falcon External API and calling `GET /showDetails`.

### Files

- [`index.html`](external-api-sample/index.html) — minimal UI with a button that fetches show details and renders them
- [`main.js`](external-api-sample/main.js) — the fetch flow: get a JWT from `generate-jwt.php`, then call the External API with `Authorization: Bearer <jwt>`
- [`generate-jwt.php`](external-api-sample/generate-jwt.php) — server-side JWT signer; HS256, payload `{ "accessToken": "<your access token>" }`

### Running it locally

1. **Get an access token and secret key** from the External API page in the Remote Falcon control panel.
2. **Edit `generate-jwt.php`** and replace the `$accessToken` and `$secretKey` placeholders with your real values. **Do not commit them.**
3. **Serve the folder with PHP** (any web server with PHP works; the simplest is the PHP built-in server):
   ```bash
   cd external-api-sample
   php -S localhost:8000
   ```
4. Open `http://localhost:8000` and click **Get Show Details**. The response renders into the page; the full payload is logged to the browser console.

### Important caveats

- **The sample signs the JWT server-side on purpose.** Your secret key must never ship to the browser. If you adapt this for production, keep the signer behind your own backend.
- **The sample uses HS256.** Match whatever the External API documentation specifies at the time you integrate.
- **`main.js` hardcodes `https://remotefalcon.com/remote-falcon-external-api`.** If you point it at a non-prod environment, update that URL.

---

## Adding things to this repo

Two flavors of contribution are in scope here. Anything else (service code, deployment manifests, real documentation) belongs in the appropriate service repo.

### Adding or updating an issue template

Templates live in `.github/ISSUE_TEMPLATE/`. Each is a Markdown file with YAML front-matter that GitHub renders into the "New Issue" picker.

To add a new template:

1. Create `.github/ISSUE_TEMPLATE/<name>.md` (e.g. `plugin_bug.md`, `external_api_bug.md`).
2. Front-matter must include:
   ```yaml
   ---
   name: Short label shown in the picker
   about: One-line description shown under the label
   title: ''                # optional: pre-filled title prefix
   labels: 'bug,plugin'     # optional: labels auto-applied to the issue
   assignees: ''            # optional: usernames auto-assigned
   ---
   ```
3. The body of the file is the issue body, with placeholders the user fills in. Keep prompts short and concrete — long templates get skipped.
4. Open a PR. Templates take effect as soon as the PR merges to `main`.

When *not* to add a new template: if a single field would distinguish it from an existing template (e.g. "this is a mobile bug"), add a checkbox or section to the existing template instead. We want few, well-known entry points — not a long picker list.

### Adding a new External API example

The `external-api-sample/` folder is intentionally minimal — one working flow in PHP + jQuery. If you want to contribute an example in another stack (Node, Python, Go, plain `fetch` without jQuery), add it as a sibling folder:

```
external-api-sample/         # existing PHP + jQuery example
external-api-sample-node/    # new Node example
external-api-sample-python/  # new Python example
```

Each example folder must:

1. **Be self-contained** — runnable with one command after the user fills in their access token and secret key.
2. **Include a `README.md`** with the run command, what the example demonstrates, and any prerequisites.
3. **Never commit real credentials** — use placeholder strings (`"<your-access-token>"`) and a comment pointing to where in the control panel the real values live.
4. **Demonstrate exactly one flow.** The current sample does `GET /showDetails`. New samples should pick one endpoint and show it well, not try to cover the whole API.
5. **Use the prod base URL** (`https://remotefalcon.com/remote-falcon-external-api`) so the example works out of the box.

Open a PR with the new folder. A maintainer will run it locally before merging.

### What does *not* belong here

- Service code, deployment manifests, infra config — these go in the service repo they affect.
- Long-form documentation — that lives at [docs.remotefalcon.com](https://docs.remotefalcon.com) and in the [`developer-files`](https://github.com/Remote-Falcon/developer-files) repo.
- Issue triage automation, labelers, project boards — handled at the GitHub org level, not in this repo.

---

## Triage

Issues filed here are triaged by the maintainer and either resolved in this repo (if it's a tracker / template / sample concern) or **transferred** to the relevant service repo using GitHub's built-in issue transfer. Comments and history follow the issue when it moves, so you don't need to re-file.

If your issue gets transferred, you'll get a GitHub notification with the new location.
