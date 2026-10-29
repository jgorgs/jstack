# Screen critique

Open with one line: who you think uses this screen and what they came here to do. Being wrong out loud beats guessing silently.

Seven passes, in order:
1. Hierarchy. Name the first three things the eye lands on and say whether the primary action is one of them. Flag competing emphasis, weak grouping, and anything loud that isn't important.
2. Spacing. Check for one consistent spacing scale, whether related things sit closer than unrelated ones, and anything cramped or stranded.
3. Type. Count the distinct sizes and weights. Flag line length past about 75 characters, tight line height on body text, running all caps, and text that truncates.
4. Color. Count the colors doing real work. Flag color as the only carrier of meaning, brand color competing with the primary action, and text or icons too light to read.
5. Interaction and states. Is the primary action obvious and in reach? Do clickable things look clickable? Then check the screen empty, loading, erroring, with one item, with two hundred, and with the longest realistic string. On a running app, trigger them for real.
6. Copy. Buttons that name the outcome, errors that say what to do next, jargon the user was never taught, redundant headers, mixed casing.
7. Accessibility. Use the quick pass from `accessibility.md`.

Then run the repo section's checklist, if it has one, and mark each item pass or fail.

Output:
- Header: the screen, who uses it, what they came to do, and what you reviewed (source, widths, themes).
- Verdict: one line.
- Fix first: numbered. Each item says what is wrong, where it sits, what it costs the user, and the specific fix.
- Worth fixing: the same shape, bulleted.
- Minor: one line each.
- Working well: two or three specific lines.
- Could not judge: what the screen wouldn't tell you.
