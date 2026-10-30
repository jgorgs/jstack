# Upstream review

JStack takes changes from Michael Denyer's [pstack-claude](https://github.com/michael-denyer/pstack-claude) by hand and never merges it. The `upstream` remote points there: `git remote add upstream https://github.com/michael-denyer/pstack-claude.git`.

Last reviewed: `1b01ed1` (pstack-claude 0.9.105)

## Procedure

1. Run `git fetch upstream`.
2. Read the entries in `git show upstream/main:CHANGES.md` above the recorded version's heading. `git log --oneline <recorded>..upstream/main` lists the commits behind them.
3. List the changes worth taking, each with one line saying why.
4. Copy those changes by hand on a branch and open a PR.
5. Update the recorded commit and version above to the `upstream/main` commit you reviewed.

`/jstack:upstream-review` runs steps 1 to 3, and steps 4 and 5 once Jake picks.
