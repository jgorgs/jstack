# UI copy pass

Inputs: the screen and who is reading it. A first-timer and a daily user need different words. Follow the repo's voice guide over these rules when the repo section names one.

1. List every string on the screen with where it sits. Missing strings count too: an empty state with no words is a finding.
2. Rewrite against these rules:
   - A button names the outcome. "Send invite" beats "Submit". "Delete 3 files" beats "OK".
   - An error says what happened and what to do next, in the user's words, with no codes unless support needs them.
   - Cut jargon the product never taught them. Internal model and feature names are the usual offenders.
   - Use one voice and one casing convention: sentence case unless the system says otherwise.
   - An empty state says what goes here and how to add the first one.
   - Keep the rewrite close to the original length so the layout still holds. Say when a fix needs more room.
3. Never change what a control does to make its label read better. Flag the mismatch instead.
4. Mark the strings you would cut. Most screens carry a line explaining something already obvious.

Output: a table with the location, the original string, your rewrite, and one clause on why. Then list the strings to cut and any you couldn't read. When you fix copy in the branch, apply the rewrite exactly as written in the table.
