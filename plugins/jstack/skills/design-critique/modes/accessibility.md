# Accessibility pass

The quick pass covers checks 1, 3, 4, and 5 and runs every time. The full pass covers all ten and runs only on its trigger in SKILL.md. Use the platform the repo section names for target sizes; default to web.

Check in this order:
1. Text contrast against the background it actually sits on, in every theme you were asked to check. Body text needs 4.5 to 1. Text at 18pt, or 14pt bold, and above needs 3 to 1. Give the ratio you measured or estimated.
2. Non-text contrast. Icons, input borders, focus rings, and chart colors need 3 to 1.
3. Target size: about 44 by 44 points on touch screens and mobile web, 24 by 24 CSS pixels minimum on desktop web, with enough space between adjacent targets.
4. Color-only meaning. Read the screen in greyscale. Anything that only made sense in color is a finding.
5. Labels. Icon-only buttons need a name. Form fields need a visible label that stays visible, so a placeholder standing in for a label is a finding.
6. Errors. Tied to the field, written in words, never carried by color alone.
7. Text scaling. What breaks at 200 percent, and whether anything that carries meaning truncates.
8. Motion. Autoplay, parallax, and looping animation with no way to stop it.
9. Keyboard. Tab through the running app. Focus order should match visual order, focus should always be visible, nothing should trap focus, and a modal should return focus when it closes.
10. Semantics. Check the DOM for real buttons and links, labelled inputs, and a sensible heading order. Screen-reader output isn't testable here, so say so.

Output: one line per check, marked pass, fail, or not testable. Every fail gives the measured or estimated value, the requirement it missed, and a specific fix, such as a darker token for a label rather than "improve contrast". Close with the two failures that block the most people.
