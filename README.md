# IniMinimo — legal documents

The published Terms of Service and Privacy Policy for the IniMinimo app, and the
source of truth for **which version a user is asked to accept**.

This repository is public on purpose: the documents people agree to should be
readable without installing anything, and the commit history is the audit trail
for "what exactly did this user accept, and when".

## Layout

```
legal/
  tos/<version>/terms-of-service.md
  privacy/<version>/privacy-policy.md
  index.json                      <- the currently required version of each document
```

## Published versions are immutable

**A directory under `legal/<doc>/<version>/` is never edited once it is
published.** A change means a new version directory.

This is the whole point. Consent is only meaningful if we can show the exact text
someone agreed to on the date they agreed to it. Editing a published file in place
destroys that — the record would say they accepted `2026-01-16` while the file at
that path said something else. Old versions therefore stay here forever, even long
after they stop being current.

## Versions are dates, and they are assigned by a human

Format: `YYYY-MM-DD`, the document's effective date.

Dates rather than semver because that is what legal documents actually use
(GitHub, Google, Atlassian, Stripe and Mozilla all date theirs; none use semver),
and because "MAJOR = backwards-incompatible" has no meaning in prose.

The version is a **deliberate human decision, never derived from a file hash or a
commit**. Fixing a typo should not re-prompt every user in the app, so a
non-material correction can ship as a new version with
`requires_reacceptance: false` — or, more usually, not as a new version at all.

## `index.json`

Per document:

| field | meaning |
|---|---|
| `version` | the version users are currently required to have accepted |
| `path` | where that version's text lives in this repo |
| `effective_date` | the date the version takes effect |
| `requires_reacceptance` | whether users on an older version must accept again before continuing |

`requires_reacceptance` is what separates a substantive change from a spelling
fix. Set it to `false` only when the change genuinely does not alter what someone
agreed to.

## How the app uses this

The backend keeps **its own copy** of these documents and serves them from a
public, unauthenticated endpoint. It deliberately does **not** fetch from GitHub
at request time: the app is gated on consent, so a GitHub outage would otherwise
mean nobody can accept and every user is locked out. GitHub is the source of
truth and the human-readable home; availability of the consent screen is ours.

## Provenance of the initial version

The `2026-01-16` documents are the text that shipped inside the Flutter app
(hardcoded in `terms_privacy_screen.dart`), extracted verbatim with no rewording.
They are recorded here as the historical record of what was in the product.
