                    USER / EVENT STREAM
                           │
                           ▼
                    ┌──────────────┐
                    │ ASR / Input  │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │ Drift Detector│
                    └──────┬───────┘
                           │
                 segment / trigger
                           │
                           ▼
                ┌────────────────────┐
                │ Context Constructor│
                │                    │
                │ transcript segment │
                │ memories           │
                │ entity digest      │
                │ people digest      │
                │ knowledge graph    │
                │ Gmail              │
                │ Slack              │
                │ Jira               │
                │ other MCPs         │
                └──────────┬─────────┘
                           │
                           ▼
                 ┌──────────────────┐
                 │   AGENT LOOP     │
                 │                  │
                 │ reason → action  │
                 │ observe → reason │
                 │       ...        │
                 └────────┬─────────┘
                          │
               ┌──────────┴──────────┐
               ▼                     ▼
        Actions / CRUD          Handoff / Prefill
               │
               ▼
        External systems
