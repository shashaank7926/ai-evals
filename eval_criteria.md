## System quality
    ├── Input
    ├── Drift
    ├── Context
    ├── Agent
    ├── Tools
    ├── State
    ├── Handoff
    └── Safety

## Final Evaluation will contain:
- score
- evidence
- summary

### Instead of evaluating entire system, we will evaluate each component of the system.
1. DriftEvaluator
2. ContextEvaluator
3. MemoryEvaluator
4. KGEvaluator
5. IntentEvaluator
6. ToolEvaluator
7. StateEvaluator
8. SafetyEvaluator
9. HandoffEvaluator
10. TaskSuccessEvaluator

### ASR
- Word Error Rate
- Entity Error Rate
- Number Error Rate
- Name Error Rate
- Timestamp accuracy

### Drift Detection
- Boundary precision
- Boundary recall
- Boundary F1
- Mean boundary error
- False drift rate
- Missed drift rate

### Context Quality
- Retrieval recall
- Retrieval precision
- Relevance
- Freshness
- Completeness
- Conflict resolution
- Context utilization

### Memory Retireval
- Retrieval correctness
- Relevance
- Freshness
- Conflict resolution
- Influence on decision