# Contributing

JStack is maintained by Jake Gorgol. It began as a fork of Michael Denyer's [pstack-claude](https://github.com/michael-denyer/pstack-claude), which ports Lauren Tan's [pstack](https://github.com/cursor/plugins/tree/main/pstack), and no longer merges either one. Worthwhile upstream changes are copied by hand through the review in [UPSTREAM.md](UPSTREAM.md).

[CONTEXT.md](CONTEXT.md) is the glossary for the terms below.

## Before you open a PR

Install the root dependencies, then run the generator and the tests:

```shell
bun install --frozen-lockfile
bun tools/generate.mjs
bun test tests/
```

The root `package.json` declares `typebox` and the optional `@earendil-works/pi-coding-agent` as peer dependencies, because Pi provides both to an extension at runtime. The root `bun.lock` pins `typebox` for the generator, which checks hook files against it, and for the tests under `tests/pi/`, which load the extension outside Pi.

The generator writes `VERSION` into all four plugin manifests and the Pi `package.json` and stamps model defaults from `plugins/jstack/models.json`. It also owns the lines under the first heading of some files: the Codex preamble on each skill with a row in the Per-skill notes table of `skills/poteto-mode/references/codex-tools.md`, and the driver-skill line on the playbooks in `DRIVER_PLAYBOOKS`. Either line anywhere else fails the generator, so a skill gets the preamble by gaining a notes row. The GitHub Copilot preamble comes the same way from the Per-skill notes table of `copilot-tools.md` and follows any Codex preamble. No skill file carries a Pi line: `poteto-mode/SKILL.md` points at `pi-tools.md` once, and a skill's Pi note is a row in that file. For Copilot it also stamps the role list in `skills/setup-jstack/scripts/sheet.awk`, the roles-by-tier table in `setup-jstack/copilot.md`, and the role-model skill list in `hooks/session-start-copilot.md` from `models.json`. It validates each skill's `name` and `description`, then generates a Codex prompt for each public skill using the [slash-command table](docs/reference.md#slash-commands).

It also copies the four files in `PORTABLE_ASSETS` into the skills-only installation and removes stale generated files. `NOTICE-skills.md` supplies the notice included with those skills.

The generator rejects missing Markdown links, links outside the skills tree, and instructions to open unreachable files. It checks for stray model names, requires a matching `CHANGES.md` heading, and validates the Codex marketplace and Claude hook paths. It also enforces these rules:

- No `commands/` directory.
- No `disable-model-invocation` on a skill.
- Every `principle-*` leaf sets `user-invocable: false`.
- Plugin agents use their namespaced `jstack:<name>` names.

CI runs `bun tools/generate.mjs --check`, which writes nothing and fails naming each generated file that is stale, missing, or orphaned, so commit the generator's output. Run the same command locally to preview CI. It checks your working tree, not the commit, so a gitignored file such as `.DS_Store` in a generated directory fails only locally, and uncommitted regenerated output passes only locally. `.pre-commit-config.yaml` runs the same command before each commit once you run `prek install` (or `pre-commit install`) in your clone.

When adding a skill, include `name` and `description` in its frontmatter. Public skills also need a row in the slash-command table. The row supplies the Codex menu description and ordering. The generator reports any skill missing a row or any row without a skill.

Change model defaults in `models.json`, never in a skill body. A role names a tier from `tiers` (`default`, `strongest`, or `panel`), so moving a tier is one edit, the `codex` block gives the Codex example for each tier, and the `pi` block maps each available family name to a Pi model ID once per Pi provider, with a `fallback` provider for sessions on any other. The generator checks the configuration's structure as it loads it (`parseModels` in `tools/generate.mjs`) and fails naming the offending role, tier, or slug. `tests/models.test.mjs` proves each malformed shape is rejected and checks that skills name every role they use. A full `claude-*` ID or a backticked available name such as `` `fable` `` outside a generated region fails the generator with its file and line.

`bun test tests/` covers the generator, the link validator, and `tests/invariants.test.mjs`, which builds fixture trees that must trip each layout invariant. One check is behavioral and lives in `tests/skill-collision-repro.sh`: it needs the `claude` CLI and API access and makes one haiku call to prove a user-typed `/plugin:name` reaches a skill with no `commands/` present. CI cannot run it, so run it locally at least once before a release.

If you touched `plugins/jstack/pi/` or `pi-tools.md`, run the Pi checks against a signed-in `pi` 1.1 as well. `bun tools/typecheck-pi.mjs` typechecks the extension under strict against the installed Pi package's own types; it uses `PI_PACKAGE_DIR` when set and otherwise looks under the global npm root, then Homebrew's `pi-coding-agent` formula (`$(brew --prefix)/opt/pi-coding-agent/libexec/lib/node_modules`), then bun's global directory (`BUN_INSTALL_GLOBAL_DIR`, then `$BUN_INSTALL/install/global`, then `$XDG_CACHE_HOME/.bun/install/global`, then `~/.bun/install/global` and `~/.cache/.bun/install/global`), as `tests/pi/catalog.test.mjs` and `tests/pi/runtime.test.mjs` do through `tools/pi-package.mjs`. CI's `Pi extension types` job installs the pinned release in the global npm root. `JSTACK_PI_LIVE=1 bun test tests/pi/live.test.mjs` drives the extension through real `pi` processes in about five minutes. It makes real model calls, on the shipped `openai` models by default. Set `JSTACK_PI_LIVE_PROVIDER=anthropic` to call the Claude models instead, or set `JSTACK_PI_LIVE_MODELS` to a `pi models:` line to replace individual models.

If you touched `plugins/jstack/hooks/`, `copilot-tools.md`, or `setup-jstack`, run `tests/copilot-smoke.sh` against a signed-in `copilot` CLI. It installs the checkout into a throwaway `HOME` and `COPILOT_HOME`, spends about twenty premium requests, and skips when `copilot` is missing. Set `SMOKE_GITHUB=<owner>/<repo>@<ref>` to repeat the plugin-file checks on a GitHub marketplace install of a pushed ref.

If you touched `skills/poteto-mode/scripts/`:

```shell
cd plugins/jstack/skills/poteto-mode/scripts
bun install --frozen-lockfile
bun run typecheck
bun test orch watch-pr check-plan.test.ts
bunx prettier@3.6.2 --check .
```

The scripts use Prettier 3.6.2 at upstream's settings in `.prettierrc.json`, CI runs the same check, and `.prettierignore` names each file the check skips and why.

`ship-pr` has one check CI cannot run. `watch-pr/live-merge-safety.mjs` creates a private repository on your `gh` account, drives `ship-pr inspect` and `cancel-pending` against a stale head, a moved base, and a retargeted base, confirms GitHub refuses a merge at a stale SHA, merges the current head, and deletes the repository. The delete needs the `gh` token's `delete_repo` scope; when it fails, the script prints the command to delete the repository by hand. Run it from the same directory before a release that changes `watch-pr/`:

```shell
bun watch-pr/live-merge-safety.mjs --live-disposable
```

If you touched a workflow, audit it before pushing:

```shell
uvx zizmor@1.29.0 --persona pedantic --min-severity low --collect all -- .
```

`--collect all` matches what CI scans. Pointing zizmor at `.github/workflows/` alone skips `dependabot.yml`, so the local run comes back clean on findings CI will fail on.

## Things that will fail CI

- **A `plugins/jstack/commands/` directory.** Claude Code renders commands and user-invocable skills in the same slash menu, so a trampoline paired with its skill duplicates every `/jstack:<name>` row ([#22](https://github.com/michael-denyer/pstack-claude/issues/22)). Codex stubs live in `plugins/jstack/.codex-plugin/prompts/`. A change copied from upstream may bring a `commands/` file; move any new stub across.
- **`disable-model-invocation` in a skill's frontmatter.** On a skill it makes the Skill tool refuse the invocation outright, which breaks the SessionStart mandate. The `principle-*` leaves use `user-invocable: false` instead.
- **Stale generated output.** The `Generated files current` job runs `bun tools/generate.mjs --check`, which fails naming each generated file to write or orphan to remove. Editing `VERSION` without regenerating, hand-editing a manifest's `version` field, or bumping without a matching `CHANGES.md` heading all land here. The same run validates `hooks/hooks.json`, the Codex hooks file `.codex-plugin/plugin.json` names, and the Copilot hooks file `.github/plugin/plugin.json` names: every `${CLAUDE_PLUGIN_ROOT}` path a command names, or `${COPILOT_PLUGIN_ROOT}` path in the Copilot file, must exist in the plugin.
- **An unresolved merge conflict.** `bun tools/generate.mjs` fails on any line under `plugins/jstack` that starts with seven `<`, `|`, `=`, or `>` followed by a space or the line end, and names the file and line. A `|` line comes from a merge under a `diff3` conflict style.
- **A missing or escaping local Markdown link.** `tools/validate-skills.mjs` resolves bare, `./`, `../`, and reference-style targets against their Markdown file. Every local target must exist inside `plugins/jstack/skills`.
- **Prose naming a plugin file the install does not carry.** The same tool resolves every backticked relative path against its Markdown file and against the plugin root. A token that lands on a real file or directory outside `plugins/jstack/skills` (`agents/comment-sicko.md`, `../../hooks/hooks.json`) fails. Tokens that resolve to nothing (placeholders, slash commands, `plugins/jstack/models.json` maintainer notes) pass. A Markdown link is caught by the link check; this covers the backticked form that is not a link.
- **A shell script that fails shellcheck.** Every `.sh` file outside `node_modules` is linted at warning severity.
- **An action pinned to a tag.** Use the full 40-character commit SHA with a version comment. A mutable tag can be force-pushed into our runners.
- **A workflow file that fails `actionlint`.** Invalid YAML, a malformed expression, an unknown runner label, or a `needs:` pointing at a job that does not exist. Run `actionlint` from the repository root.
- **A Markdown correctness error.** Reversed link syntax, an empty link target, a missing image alt, a fragment link to a heading that is not there, or an undefined or unused reference definition. The rule set is deliberately correctness-only and lives in `.markdownlint-cli2.jsonc`. Run `npx --yes markdownlint-cli2@0.18.1 '**/*.md'`.
- **A broken relative link in any Markdown file.** The link job resolves file and fragment targets offline and never touches a remote URL, so external link rot cannot fail your PR. Run `lychee --offline --include-fragments --exclude-path node_modules --exclude-path .git '**/*.md'`.

## Dependency updates

Dependabot keeps the pinned action SHAs current. The vendored scripts' one runtime dependency (`commander`) is pinned in their `bun.lock`; `osv-scanner` scans every `bun.lock` weekly, so a CVE surfaces. The root `bun.lock` pins `typebox`, which the generator and the Pi extension tests import. If you bump it by hand, run `bun install` and commit the resulting `bun.lock` in the same change.

## Releasing

Plugin auto-update installs **by version number**, not by tracking `main`. A skill fix merged without a version bump is inert on every installed copy, because the updater sees the same version it already has and does nothing.

So: any PR that changes skill behavior either bumps the version itself or is followed by a release PR that does. The bump is three steps: edit the root `VERSION` file, add a `CHANGES.md` entry under a `## <version>` heading describing what changed and why, and run `bun tools/generate.mjs` to stamp the manifests. Forgetting any of the three fails CI. Run the full invariant script (including the behavioral leg) before merging.

## Commit and PR style

- Explain what changed and why. The diff already shows how.
- One concern per PR. A behavior fix and a refactor in the same diff are two separate reviews, and reviewing them together means doing neither properly.
- Claim only what you verified, and name the check. "52/52 bun tests pass" beats "tests pass"; "did not run the behavioral leg" beats silence.

## Reporting bugs

Include the jstack version, the Claude Code (or Codex, Pi, or GitHub Copilot) version, and the reproduction steps. [#22](https://github.com/michael-denyer/pstack-claude/issues/22) is the model to copy: it named versions, gave numbered steps, and included the experiment that isolated the cause.

Reproduce the bug on the latest release before you file it, because only that release receives fixes. The [bug report form](.github/ISSUE_TEMPLATE/bug_report.yml) asks for each of these fields. An issue filed through `gh` or the API skips the form, so give the same fields in the same order.
