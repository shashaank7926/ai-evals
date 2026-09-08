TRACE: conversation_id=xxx
│
├── input
│    ├── ASR
│    │    ├── audio_duration
│    │    ├── transcript
│    │    └── confidence
│    │
│    └── drift_detection
│         ├── segment_start
│         ├── segment_end
│         ├── drift_type
│         └── drift_confidence
│
├── context_build
│    │
│    ├── memory_retrieval
│    │    ├── query
│    │    ├── candidates
│    │    └── selected_memories
│    │
│    ├── entity_digest
│    │
│    ├── people_digest
│    │
│    ├── knowledge_graph
│    │
│    ├── gmail_search
│    │
│    ├── slack_search
│    │
│    └── jira_search
│
├── agent
│    │
│    ├── decision_1
│    │    ├── intent
│    │    ├── proposed_action
│    │    └── confidence
│    │
│    ├── tool_call_1
│    │    ├── MCP
│    │    ├── tool
│    │    ├── arguments
│    │    ├── result
│    │    └── latency
│    │
│    ├── observation_1
│    │
│    ├── decision_2
│    │
│    └── tool_call_2
│
├── state_change
│    ├── before
│    ├── action
│    └── after
│
└── outcome
     ├── response
     ├── handoff
     ├── prefill
     └── user_feedback
