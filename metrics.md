# Metrics

| Layer       | Metric                  |
| ----------- | ----------------------- |
| ASR         | WER / entity WER        |
| Drift       | boundary F1             |
| Context     | retrieval recall@k      |
| Memory      | useful-memory precision |
| KG          | relationship accuracy   |
| Agent       | intent accuracy         |
| Agent       | action accuracy         |
| Tool        | tool-selection accuracy |
| Tool        | argument correctness    |
| Execution   | success rate            |
| State       | intended-state accuracy |
| Safety      | unsafe-action rate      |
| Outcome     | task success            |
| UX          | unnecessary-action rate |
| Performance | p95 latency             |
| Economics   | cost / successful task  |


### Context quality depends upon:

Transcript
+
Memory
+
Knowledge graph
+
Entity digest
+
People digest
+
Gmail
+
Slack
+
Jira
+
MCP tools

# Evaluate

- memory retrieval precision
- knowledge graph relationship accuracy
- knowledge graph usefulness
- agent tool selection accuracy
- agent tool argument correctness

#### Evaluate the agent by:

- deterministically
- LLM as a judge
- HITL

#### Success criteria for a task:

1. Intent Correct
2. Required Context Available
3. Correct Action
4. Correct Tool
5. Correct Arguments
6. Expected State Achieved
7. No Unauthorized Side Effects

# Lets use Langfuse as it provides:

- Better for entire agent behavior evaluation
- Better support for multi-step traces
- Better support for LangChain and LangGraph in future

# No Phoenix because:

- it is more inclined towards single-step traces
- RAG or llm intensive tasks and not agentic tasks


# Keep the app architecture tied to OpenTelemetry so that we can use it for alternative approaches. Use Deepevals for evaluating LLMs and agents in CI pipelines.