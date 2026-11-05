### Investigation

**You own the answer. Plan, route, write.**

Investigation requests are read-only. They produce a cited explanation or a recommendation, not a code change.

1. Route through the **how** skill. For motivation questions, also route through the **why** skill. A diagnosis forwarded from another agent, a bot, or an alert ("ops/data, close it") is a hypothesis. Before you agree to close or dismiss it, run the cheapest live query that asks whether this path has ever succeeded in production. A lookup that has never matched is a design bug, not drifted data.
2. Throughput checkpoint stays one line: `throughput checkpoint: n/a, read-only investigation`.
3. Produce the `how`-shaped output (Overview / Key Concepts / How It Works / Where Things Live / Gotchas), or a recommendation with a tradeoffs table if the request is a decision between alternatives.
4. Apply the **unslop** skill to the reply.

No PR, no babysit, no `architect` unless the investigation precedes a code change. If it does, hand back to the user and re-route to Bug fix or Feature.

**Reply:** open with a plain-language verdict and what users lose. Next, say outright what you have and have not changed in code, data, and config. Function names and error codes come after both. Then the investigation output. For "are we sure?" answers, include your real judgment with reasons. Push back if the premise is wrong (see Autonomy).
