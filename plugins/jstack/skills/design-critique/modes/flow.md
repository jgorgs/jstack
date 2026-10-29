# Flow critique

Inputs: the screens in order and the goal the person is trying to reach by the end. Walk the flow in the running app, not from screenshots alone.

1. Write the path in one line: the step names and the count. The count is often the finding.
2. For each step, note what the user has to know, decide, or type before they can move on.
3. Look for path problems a single-screen review misses:
   - something asked before the user sees why it matters
   - the same information asked for twice
   - a decision with no way to change it later
   - a dead end with no way back
   - no sense of progress or length
   - input lost on error, reload, or going back
   - anything that shifts between steps, such as button position, labels, or type scale
4. Name the step most likely to lose people and say why.
5. Run the screen passes on that step only, plus any step the diff changed.
6. Say what you would cut. Most flows carry a step that can be deferred or dropped.

Output:
- The path: one line with the steps and the count.
- Where you lose people: the step and why.
- Per step: findings with severity, or "nothing material" for clean steps.
- Cut or defer: steps that don't need to be there.
- Fix first: the three changes that most help people finish.

Never guess at a step you couldn't reach. Name it and review what you have.
