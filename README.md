# renovate-config — tractat-us org-wide Renovate preset

Shared [Renovate](https://docs.renovatebot.com/) rules for every repo in the org.
`default.json` is the whole thing: what auto-merges, what waits for a human, and
what Renovate should leave alone entirely.

Repos opt in with a minimal `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>tractat-us/renovate-config"]
}
```

## Why this repo is public

The Mend-hosted Renovate app gives a **public** repo a token scoped to *that repo
alone*, so that a leaked token can't reach the rest of the org. The consequence:
a public repo **cannot read a preset from a private or internal repo** — Renovate
fails the run with `Cannot find preset's package` and stops opening PRs entirely.
A private repo reading a public preset is fine; only that one direction is blocked.
See [Renovate's private-packages docs](https://docs.renovatebot.com/getting-started/private-packages/).

These rules contain no secrets — they're dependency policy — so the preset lives
here, in the open, where public and private repos alike can reach it.

**Keep it that way.** Anything genuinely private goes in `tractat-us/.github`'s
own `default.json`, which extends this one and is only reachable from private and
internal repos.

## What's in it

| Rule | Effect |
|------|--------|
| `config:recommended` + `platformAutomerge` | Renovate's baseline, merged by GitHub rather than by a Renovate commit. |
| Non-major auto-merge | `minor`/`patch`/`pin`/`digest`/`lockFileMaintenance` land on their own once CI passes. **Requires the extending repo to have a required status check** — without one, auto-merge lands ungated. Majors always stay manual. |
| Skip `us.tractat.**` | Our own packages are published by us, on our schedule; Renovate cloud has no credentials for them anyway. |
| 30-day hold on the Gradle wrapper | Lets AGP/Kotlin compatibility settle before we move. |
| Never auto-merge Compose | A green CI run doesn't check layout. These need a WASM-preview or roborazzi look first. |
| AGP majors need dashboard approval | No PR opens until someone ticks the box, because the fallout (Kotlin/Compose compat, deprecations) is wide. |

A repo that needs to differ overrides the rule locally rather than forking the
preset — see `tractat-us/fireworks-docs`, which turns auto-merge back off because
its only CI check is path-filtered.
