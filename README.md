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

### Return errors the agent can use
"Something went wrong" is a dead end. Tool errors should include a stable code, the field or resource that failed, whether retrying is safe, and the smallest next action that could fix it. Agents recover better from structured friction than from apologetic prose.

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

### Make the finish condition a check, not a vibe
An agent should stop because something external confirms the work is done, not because the model ran out of things to say or hit its loop ceiling. Write the stopping condition as a verifier the loop can call — tests pass, the schema validates, the required fields are populated, the diff applies cleanly — and end the moment it's satisfied. A budget cap is a safety net, not a definition of done. Without an explicit finish check, agents either quit early on a plausible-looking answer or grind to the ceiling, and afterward you can't tell which one happened.

### Scope permissions like product surface area
An agent with a broad token is a production feature, whether you meant to ship it or not. Give each workflow the narrowest credentials and tool set it needs, then fail closed when scope is missing. Prompts ask nicely; permissions enforce.

### Make uncertainty actionable
A confidence score is only useful if it changes behavior. Ask the agent to return what it knows, what it is assuming, and the next check that would reduce risk. Then route low-confidence outputs to retrieval, tests, or human review instead of letting them sound authoritative.

### Separate planning from acting
Let the model sketch the next few steps before it touches tools, then execute one step at a time with fresh observations. Plans catch missing context; stepwise execution catches reality changing under the plan.

### Capture receipts, not vibes
When an agent finishes a task, make it return the concrete proof: commit hashes, URLs, request ids, screenshots, test commands, or the exact diff it applied. Summaries are helpful for humans, but receipts are what let the next agent verify, resume, or roll back without guessing.

### Design for resume, not restart
Long-running agents should checkpoint after each irreversible step: input hash, cursor, last successful side effect, and the next safe action. If the process dies halfway through, the correct behavior should be “continue from here,” not “rerun the whole thing and hope idempotency saves us.”

### Treat cancellation as a product path
A stuck agent should have a boring way out: stop accepting new side effects, persist what already happened, and return the next safe owner action. If cancel just means killing the process, you are one timeout away from half-sent emails, orphaned jobs, or a retry storm.

### Normalize tool outputs at the boundary
Third-party APIs return whatever shape their product team needed, not what your agent can reason over safely. Convert raw responses into small, stable structs with the fields the workflow actually needs, plus a source URL or request id. The prompt should see facts, not an SDK dump.

### Version prompts with the code
A prompt change can break a workflow as easily as a code change. Keep prompts, tool schemas, eval cases, and migration notes in the same review path so you can trace why behavior changed instead of guessing from production logs.

### Pin the model like a dependency
Providers ship silent updates and deprecations, so a workflow that passed yesterday can regress overnight with zero code changes. Pin the exact model id and sampling params in config, and re-run your eval set whenever you bump a model or the provider ships one under you. A model upgrade is a dependency upgrade: it belongs behind a diff and a green eval run, not an act of faith.

### Make failure cheap and visible
Agents will fail. Design so failures are reversible (dry-runs, drafts, approvals) and loud (alerts, traces) rather than silent and expensive.

### Report partial progress precisely
Most useful agent runs are not binary successes. Have them separate completed, skipped, blocked, and risky items in the final receipt, with enough detail for the next run to continue. "Done except a few edge cases" is where follow-up work goes to die.

---

These notes grow as I learn. Spot something you disagree with? Open an issue — I like being argued with when there's a better answer on the other side.

## License

[MIT](LICENSE)
