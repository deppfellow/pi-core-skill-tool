# pi-core-skill-tool (patched fork)

Patched fork of [`@arhen/pi-core-skill-tool`](https://github.com/arhen/pi-extensions) (`packages/core/pi-core-skill-tool`, MIT by arhen), maintained at `deppfellow/pi-core-skill-tool`.

Same extension, one fix: the catalog-strip regex now understands pi >= 0.99's `<skills>` system-prompt section.

## Why the fork

pi 0.99 changed the system prompt: `formatSkillsForPrompt()` output is `.trim()`-ed and wrapped in a `<skills>...</skills>` named section (`dist/core/system-prompt.js`). The upstream regex was anchored on the leading blank line that no longer exists, so stripping silently failed and the full skill catalog (with `<location>` paths) leaked back into every prompt - models then load skills through the fenced `read` tool instead of the extension's lazy `skill` tool.

Upstream tracking: [arhen/pi-extensions#8](https://github.com/arhen/pi-extensions/issues/8).

## What changed vs upstream 1.0.10

- Single `\n\n`-anchored regex replaced with a two-pattern loop:
  1. pi >= 0.99 `<skills>` wrapper shape (whole wrapper removed, so no empty section is left)
  2. legacy inline shape for pi <= 0.98, with the `Use the read tool` / `Use bash` alternation (upstream hardcoded the read variant, which also failed on bash-only setups)
- `peerDependencies` range widened to `>=0.84.2` (upstream declared `^0.84.2`, excluding current pi)

Verified on pi 0.99.1 and 1.0.0: catalog absent from the final prompt, `skill` tool loads skill bodies E2E.

## Install (pi)

```
git:github.com/deppfellow/pi-core-skill-tool@v1.0.10-fix.1
```

Git tags are pinned: `pi update` reconciles the checkout but never moves the ref.

## Upstream policy

Rebase this onto upstream releases as they ship. Once upstream publishes a release with the issue #8 fix, switch back to `npm:@arhen/pi-core-skill-tool` and retire this fork.
