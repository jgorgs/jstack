---
name: upstream-review
description: List the pstack-claude changes since the commit recorded in a JStack clone's UPSTREAM.md that are worth copying into JStack, each with a one-line reason. Use for /upstream-review, "review upstream", or "what's new in pstack".
---

# Upstream review

Run from a clone of `jgorgs/jstack`. `UPSTREAM.md` at its root records the last pstack-claude commit reviewed and the procedure. Read it first. If the working directory is not such a clone, ask for its path.

1. Run `git fetch upstream`. If the remote is missing, add it as `UPSTREAM.md` says.
2. Read the `upstream/main:CHANGES.md` entries above the recorded version's heading, and `git log --oneline <recorded>..upstream/main`. When an entry is unclear, read the commit's diff.
3. Reply with the list: each change worth taking, its upstream commit, and one line saying why. Then the changes you passed over, one line each. When nothing is new, say so and stop.

Stop there. The list is the output. Open no branch or PR until Jake picks from it.

When Jake picks, copy each chosen change by hand on a branch off `origin/main`, adapting `pstack` names to `jstack` and `poteto` names to `jedi` (`poteto-mode` to `jedi-mode`, `poteto-help` to `jedi-help`, `poteto-agent` to `jedi-agent`), and open a PR. Update the recorded commit and version in `UPSTREAM.md` in the same PR. When he takes nothing, the update is the whole PR.
