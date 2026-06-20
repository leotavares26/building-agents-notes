# Building Agents — Field Notes

Short, opinionated notes from building and shipping LLM agents. No fluff, no "the future of AI" takes — just things that cost me time so they don't cost you yours.

> By [Leo Tavares](https://github.com/leotavares26). Longer write-ups live on my blog: [scale-agents.hashnode.dev](https://scale-agents.hashnode.dev/).

## Notes

### Start with a workflow, not an agent
If you can draw the steps on a whiteboard, hardcode them. "Agentic" autonomy is a cost, not a feature — pay for it only when the path genuinely can't be known ahead of time.

### Your eval set is the product
The teams that move fastest aren't the ones with the cleverest prompts; they're the ones who can tell, in minutes, whether a change made things better or worse. Build 20–50 real examples before you tune anything.

### Tracing beats print statements
The first time an agent does something baffling in production, you'll wish you had every prompt, tool call, and token logged. Add tracing on day one, not after the first incident.

### Tool design is API design
Models call tools the way junior developers read docs: literally and optimistically. Tight schemas, clear names, defensive validation, and helpful error messages do more for reliability than a bigger model.

### Route work by failure cost
Model routing should start with the blast radius, not the benchmark chart. Cheap, reversible steps can use faster models and loose checks; expensive side effects deserve stronger models, tighter contracts, and a human checkpoint.

### Treat side effects as commits
Before an agent changes the world, make it assemble the exact artifact first: the email body, SQL query, API request, file diff, or invoice action. Verify that artifact, attach an idempotency key when the system supports one, then execute once. Vague instructions plus direct tool access are how duplicate charges and mystery emails happen.

### Make handoffs explicit
If one agent hands work to another, write the contract down: input, owner, expected output, deadline, and what to do when it fails. "Please investigate" is not a handoff; it's context leakage with a Slack notification attached.

### Context is a budget, spend it deliberately
Stuffing everything into the prompt "just in case" makes agents slower, costlier, and *worse* — relevant signal gets buried. Retrieve the slice you need; drop the rest.

### Keep state boring
Agents get weird when state only lives in chat history. Store durable facts in plain structs or tables with names that match the workflow, then rebuild the prompt from that state. If you can't diff or replay it, debugging becomes archaeology.

### Bound your loops
Every retry, reflection, or replan needs a hard ceiling. Unbounded loops are how a $0.02 task becomes a $40 one while you're asleep.

### Scope permissions like product surface area
An agent with a broad token is a production feature, whether you meant to ship it or not. Give each workflow the narrowest credentials and tool set it needs, then fail closed when scope is missing. Prompts ask nicely; permissions enforce.

### Make uncertainty actionable
A confidence score is only useful if it changes behavior. Ask the agent to return what it knows, what it is assuming, and the next check that would reduce risk. Then route low-confidence outputs to retrieval, tests, or human review instead of letting them sound authoritative.

### Capture receipts, not vibes
When an agent finishes a task, make it return the concrete proof: commit hashes, URLs, request ids, screenshots, test commands, or the exact diff it applied. Summaries are helpful for humans, but receipts are what let the next agent verify, resume, or roll back without guessing.

### Make failure cheap and visible
Agents will fail. Design so failures are reversible (dry-runs, drafts, approvals) and loud (alerts, traces) rather than silent and expensive.

---

These notes grow as I learn. Spot something you disagree with? Open an issue — I like being argued with when there's a better answer on the other side.

## License

[MIT](LICENSE)
