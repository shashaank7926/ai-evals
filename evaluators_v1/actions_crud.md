# Evals on Langfuse

1. `action_extraction_completeness` - Measures whether actions_crud captures all meaningful obligations, commitments, requests, and follow-ups from the conversation without silently omitting actionable content.

    ```
    Evaluate how completely the actions_crud output captures all actionable
    obligations expressed in the input conversation.

    The system should identify every meaningful:
    - commitment
    - request
    - delegated task
    - reminder
    - promised action
    - waiting-on item
    - concrete "we need to / let's / we should" obligation
    - explicit follow-up action
    - distinct defect, bug, or fix request

    Compare the conversation against output.operations.

    Do NOT penalize the system for correctly ignoring:
    - casual conversation
    - opinions without an obligation
    - descriptions of past actions with no remaining obligation
    - purely informational statements
    - hypothetical actions that are clearly not commitments

    The important requirement is recall: the system should not silently drop
    real obligations.

    Consider:
    - Were all distinct actions extracted?
    - Were actions hidden inside conversation or grouped facts recovered?
    - Were commitments by speakers other than the user captured?
    - Were multiple actions in a single sentence separated when necessary?
    - Were waiting_on obligations captured?
    - Were updates to existing actions recognized?

    Scoring:
    1.0 = All meaningful actionable obligations were captured
    0.9 = Nearly all captured; only a very minor omission
    0.75 = One meaningful obligation omitted
    0.5 = Multiple meaningful obligations omitted
    0.25 = Most actionable content was missed
    0.0 = Almost no real actions were extracted

    Return only a number between 0 and 1.

    System prompt & user prompt: {{input}}
    Assistant output: {{output}}
    ```

2. `action_groundedness` - Measures whether every extracted action, fact, owner, deadline, and evidence quote is supported by the actual conversation.
    ```
    Evaluate whether every action operation in the output is grounded in what
    was actually said in the input conversation.

    For each operation, verify:
    - The action is supported by the transcript.
    - The title and description reflect what was actually requested or committed.
    - The owner is supported by the conversation.
    - The action is not invented from context alone.
    - Facts are supported by the conversation.
    - The evidence_quote corresponds to the action.
    - The evidence_quote is faithful to the original speaker's words.
    - The system does not convert background information into an obligation.

    The system may infer what needs to be DONE, but must not invent what is TRUE.

    Penalize:
    - hallucinated actions
    - invented commitments
    - unsupported facts
    - unsupported deadlines
    - invented ownership
    - actions inferred only from related memories or digital context
    - actions whose evidence does not support the operation

    Score the overall proportion and severity of operations that are properly
    grounded.

    Scoring:
    1.0 = All operations are strongly grounded in explicit conversation evidence
    0.9 = Very minor grounding issue
    0.75 = One operation has a noticeable grounding problem
    0.5 = Several operations contain unsupported information
    0.25 = Many operations are weakly grounded or invented
    0.0 = Output is largely unsupported by the conversation

    Return only a number between 0 and 1.

    System prompt & user prompt: {{input}}
    Assistant output: {{output}}
    ```

3. `action_routing_accuracy` - Measures whether extracted actions are correctly classified as reminder, tool_workflow, handoff, or waiting_on based on ownership and available capabilities.

    ```
    Evaluate whether each extracted action is routed to the correct action kind.

    The available kinds are:

    - reminder:
    Something the user themselves must perform, or something they explicitly
    asked to be reminded about.

    - tool_workflow:
    Something that can genuinely be completed using the connected tools,
    including multi-step tool workflows.

    - handoff:
    Work that cannot be completed by any available combination of connected
    tools and therefore requires external/manual work.

    - waiting_on:
    Something another person owes or must provide to the user.

    For each operation, determine whether the selected kind is appropriate given:
    - who must perform the work
    - what the work requires
    - what tools are available
    - whether another person owes something
    - whether the user explicitly requested a reminder

    Pay particular attention to the TOOLS FIRST rule:
    if connected tools can complete the work, it should generally be a
    tool_workflow rather than a handoff.

    Also check that:
    - another person's promise is not incorrectly assigned to the user
    - a user-owned task is not incorrectly classified as waiting_on
    - a tool-completable task is not incorrectly classified as handoff

    Scoring:
    1.0 = All action kinds are correctly classified
    0.9 = Very minor routing issue
    0.75 = One meaningful classification error
    0.5 = Several routing errors
    0.25 = Routing is frequently incorrect
    0.0 = Action kinds are fundamentally misclassified

    Return only a number between 0 and 1.

    System prompt & user prompt: {{input}}
    Assistant output: {{output}}
    ```