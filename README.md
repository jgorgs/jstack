# JStack

JStack is Jake Gorgol's opinionated agent skill stack for Claude Code, Codex, Pi, GitHub Copilot and other agent harnesses. It grew from Lauren Tan's [pstack](https://github.com/cursor/plugins/tree/main/pstack), an opinionated Cursor skill stack that improves agent outcomes, by way of Michael Denyer's [pstack-claude](https://github.com/michael-denyer/pstack-claude) port. JStack is maintained on its own. Changes from pstack-claude are reviewed and copied by hand, as [UPSTREAM.md](UPSTREAM.md) describes.

Tell `jedi-mode` your goal and it will invoke the correct workflow for the task. It keeps your code concise, simple and verified.

For concurrency bugs and invariants that tests cannot reach, see the separate [agent-formal-verify](https://github.com/michael-denyer/agent-formal-verify) plugin, which adds TLA+ model checking and Lean proofs.

## Install

### Claude Code

Run in Claude Code:

```text
/plugin marketplace add jgorgs/jstack
/plugin install jstack@jstack
```

### Codex

Run in your terminal:

```shell
codex plugin marketplace add jgorgs/jstack
codex plugin add jstack@jstack
```

### Pi

Run in your terminal:

```shell
pi install git:github.com/jgorgs/jstack
```

The package loads the skills and the JStack Pi extension, which adds the subagent, question, and wake-up tools the skills use, plus `/loop` and the routing instruction. Invoke a skill with `/skill:<name>`.

### GitHub Copilot

Run in your terminal:

```shell
copilot plugin marketplace add jgorgs/jstack
copilot plugin install jstack@jstack
```

This installs JStack for the Copilot CLI and the GitHub Copilot app, which share `~/.copilot`. Start a new session afterwards. Copilot ships no default JStack models, so the first skill that needs one runs `setup-jstack` to pick from the models your account lists, and later sessions reuse that choice.

The Copilot build is tested on Copilot CLI 1.0.87 through 1.0.92. On those versions the routing hook's context reaches the session alongside other plugins' session-start context. If a later version keeps only one plugin's context, `setup-jstack` offers a [standing instruction](plugins/jstack/skills/setup-jstack/copilot.md#wire-it-in) for `~/.copilot/copilot-instructions.md` instead. On 1.0.92, once the CLI caches its computer-use experiment assignment, `copilot -p` sessions list no plugin skills and a `skill` call returns "Skill not found". Interactive sessions, the hooks, and the agents are unaffected.

Run `setup-jstack` to change model defaults, set a reasoning effort per role (for example `arena runners: opus @xhigh, fable @max`, which Claude Code dispatches through the plugin's `jstack:effort-<level>` or `jstack:jedi-agent-<level>` agents; roles without a level keep the session's effort unless the sheet's `default effort` line names one), or turn automatic routing off. The plugin installs the routing hook on Claude Code, Codex, and GitHub Copilot; Codex asks you to trust it through `/hooks` before it runs. On Pi the extension injects the same routing instruction. In Claude Code and the Copilot CLI, use `/jstack:setup-jstack`.

For Prime Agent, OpenCode, Gemini CLI, or skills-only installs for any harness, see [shared installation](docs/reference.md#shared-skills-installation).

## Getting started

```text
Use jedi-mode to fix the search filter resetting when I change pages.
```

For a bug, it reproduces the failure, uses `how` and `why` to investigate, delegates the fix, then reruns the failing case. If the fix crosses a function boundary, it brings in `architect` before implementation. You receive the fix and the failing and passing evidence.

[Other playbooks](plugins/jstack/skills/jedi-mode/SKILL.md#playbooks) cover planning, features, refactoring, performance issues, investigations, prototypes, PR maintenance, shipping, and longer projects.

## Details

- [Skills and slash commands](docs/reference.md#slash-commands)
- [Runtime setup](docs/reference.md#runtime-support)
- [Models and dependencies](docs/reference.md#configuration-and-dependencies)
- [Maintenance and attribution](docs/reference.md#maintenance)

## Data handling

JStack has no server or telemetry. Anything its skills ask your agent to read, including session transcripts, goes to your model provider. Scripts run locally, and PR tools use your GitHub CLI login.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for the checks every change runs. Report vulnerabilities privately as described in [SECURITY.md](SECURITY.md).

## License

JStack, including its modifications and additions, is [MIT-licensed](LICENSE), © 2026 Jake Gorgol. Original pstack © 2026 Lauren Tan; the pstack-claude port © 2026 Michael Denyer; imported cursor-team-kit and thermos skills © 2026 Cursor. See [LICENSE-cursor-team-kit](LICENSE-cursor-team-kit), [LICENSE-thermos](LICENSE-thermos), and [NOTICE.md](NOTICE.md).
