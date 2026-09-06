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
  childrens-notice/<version>/childrens-privacy-notice.md
  dmca/<version>/copyright-and-takedown.md
  index.json                      <- the current version of each published document
```

Not every document here is a document anyone accepts. `tos` and `privacy` are
the two the consent gate enforces; `childrens-notice` and `dmca` are
**published, not accepted** — served so they have a stable version string and a
public home, never added to the acceptance flow. `index.json` lists all four;
which ones are gated is decided in the backend, not here.

🔴 **The DMCA designated-agent block is a federal artifact, not copy.** The
name, postal address and email in `dmca/` must match the Copyright Office
record for registration DMCA-1079191 character for character:
`dmca@iniminimo.ai`, `INIMINIMO LLC, 5830 E 2nd St Ste 7000, Casper, WY 82609`,
**with no `c/o` line**. 17 U.S.C. §512(c)(2) makes the agent's reachability the
condition of the safe harbour, so an edit that merely tidies it — making the
address consistent with the other contact addresses, or routing it to
`privacy@` — breaks the protection the filing pays for. Counsel's own 2026-08-28
draft made both of those edits; they were reverted deliberately. Change it only
against the filing.

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
commit**. Fixing a typo should not re-prompt every user in the app — so a
non-material correction ships **not as a new version at all**. If it is worth a
new version string, it is worth re-accepting: see the standing rule below.

## `index.json`

Per document:

| field | meaning |
|---|---|
| `version` | the version users are currently required to have accepted |
| `path` | where that version's text lives in this repo |
| `effective_date` | the date the version takes effect |
| `requires_reacceptance` | whether users on an older version must accept again before continuing |

### 🔴 Standing rule: a version change is ALWAYS a re-acceptance

**Ronen, 2026-09-06: "requires_reacceptance needs to always be set to true if we
change — need to be a hard rule that that is always true."**

So `requires_reacceptance` is **not a per-change judgement call**. When a
document's version changes, the flag is `true`, in the same commit, for **every**
document whose version changed — including `childrens-notice` and `dmca`, where
the flag has no runtime effect today because those documents are published but
not gated. Setting it uniformly is the point: the field stops being something to
argue about, and a document that later *becomes* gated does not inherit a stale
`false`.

The lever for "this change should not re-prompt anyone" is therefore **not
issuing a new version**, not issuing one with the flag off.

⚠️ **This rule is not machine-enforced.** This repository has no CI. Nothing
fails if a future change bumps a version and leaves the flag `false` — it is a
review responsibility until an enforcing check exists.

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
