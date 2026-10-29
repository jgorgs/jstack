# Compare versions

Inputs: two or more variants of a screen, or the approved mock, production, and the branch for the same screen.

1. Confirm they are the same screen doing the same job. If not, critique them separately and say so.
2. Say what actually changed, in plain words, before you judge anything.
3. Against the approved mock: name each drift and whether it looks intended. Don't re-argue what Jake approved.
4. Against production: run the screen passes only where the versions differ. Don't re-litigate what both share unless it's a blocker. A regression is at least worth fixing.
5. If the screen has an open-fix list, walk it: cleared, still open, or newly introduced. Mark an item cleared only when you see it fixed.
6. For variants, pick one to ship and give the reason in terms of the user's job, not taste. A tie is a real answer. When the difference is cosmetic, say which is cheaper to ship.

Output:
- What changed: the visible differences.
- Drift from the mock: each one, intended or not.
- Ship: which version, and the one reason. Variants only.
- Why: two to four lines, each tied to a specific difference.
- True of both: anything worth fixing that neither version solved.

Never rank on preference. When both versions fail the same way, say that first and pick second. When a fix clears an existing tracker item, note it in the PR body as ready to close. Never close one without Jake's yes.
