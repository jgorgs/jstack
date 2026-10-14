# JStack

JStack is Jake Gorgol's opinionated skill stack for Claude Code, Codex, and other agent harnesses. It grew from Lauren Tan's [pstack](https://github.com/cursor/plugins/tree/main/pstack) by way of Michael Denyer's [pstack-claude](https://github.com/michael-denyer/pstack-claude) port: the same skills, with Cursor primitives translated to each runtime's tools.

Tell `jedi-mode` your goal and it invokes the workflow that fits: reproduce and root-cause a bug, sketch a design with `architect`, race candidates in `arena`, review a diff with `interrogate`, cut prose with `unslop`. It keeps code concise, simple, and verified, and it reports what it checked.

## What it contains

- Skills: Markdown instructions the agent reads. Public ones appear as `/jstack:<name>` slash commands.
- Agents: `jstack:jedi-agent` and `jstack:comment-sicko`, plus one agent per reasoning-effort level.
- A SessionStart hook that injects the jedi-mode routing mandate unless your `jstack-models.md` sheet turns it off.
- A Pi extension that supplies the subagent, question, and wake-up tools, `/loop`, and the same routing mandate when the repository is installed as a Pi package.
- Local scripts for watching and shipping pull requests, orchestrating multi-phase plans, and auditing worktrees.

## Data handling

JStack has no server or telemetry. Anything its skills ask your agent to read, including session transcripts, goes to your model provider. Scripts run locally, and PR tools use your GitHub CLI login.

## Links

- [Skills, slash commands, runtime setup, and model configuration](https://github.com/jgorgs/jstack/blob/main/docs/reference.md)
- [Issues and support](https://github.com/jgorgs/jstack/issues)
- [Security policy](https://github.com/jgorgs/jstack/blob/main/SECURITY.md)

## License

MIT for JStack and its additions, © 2026 Jake Gorgol. Original pstack © 2026 Lauren Tan; the pstack-claude port © 2026 Michael Denyer; imported cursor-team-kit skills © 2026 Cursor. See [NOTICE.md](https://github.com/jgorgs/jstack/blob/main/NOTICE.md).
