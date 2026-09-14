# Evals on Langfuse

1. `transcript_faithfulness` - Measures whether the rewritten transcript preserves the original transcript without paraphrasing, adding, removing, or changing meaning.

    ```
    Evaluate whether the rewritten_transcript faithfully preserves the original raw transcript.

    The model is a RECORDER, not a paraphraser. The rewritten transcript should preserve:
    - The original meaning and wording
    - Numbers, dates, amounts, names, and other factual details
    - The original order of content
    - Spoken phrasing where possible

    Only permitted changes include:
    - Clear ASR/proper-noun corrections when supported by the sound-alike gate
    - Obvious speaker-boundary corrections
    - Removal of fillers where explicitly allowed

    Do NOT reward:
    - Paraphrasing
    - Rewording for better grammar
    - Reordering
    - Adding information
    - Removing meaningful content
    - Changing numbers, dates, amounts, or intent

    Compare the raw transcript in the input against output.rewritten_transcript.

    Return a score from 0 to 1:
    1.0 = Completely faithful; only permitted corrections were made
    0.75 = Minor harmless deviation
    0.5 = Noticeable meaning/wording changes
    0.25 = Major distortion or unsupported changes
    0.0 = Rewritten transcript substantially changes or invents the original content

    System prompt & user prompt: {{input}}
    Assistant output: {{output}}
    ```

2. `summary_groundedness` - Measures whether the generated memory card summary contains only claims supported by the current conversation.

    ```
    Evaluate whether the generated card summary is fully grounded in the current conversation transcript.

    The summary must contain only information that is explicitly supported by the transcript.

    Check every factual claim, interpretation, outcome, and open question.

    Penalize:
    - Invented facts
    - Inferred intentions
    - Assumed motivations
    - Information from outside the current conversation
    - Conclusions not supported by the transcript
    - Open questions that were not actually implied by the conversation
    - Overinterpretation of ambiguous or garbled speech

    The summary should accurately represent what was actually said, especially when the transcript is ambiguous.

    Return a score from 0 to 1:
    1.0 = Every claim is directly supported by the transcript
    0.75 = Mostly grounded with very minor interpretation
    0.5 = Some unsupported claims or overinterpretation
    0.25 = Significant hallucination or inference
    0.0 = Summary is largely unrelated to or unsupported by the transcript

    System prompt & user prompt: {{input}}
    Assistant output: {{output}}
    ```

3. `entity_accuracy_groundedness` - Measures whether extracted entities are explicitly mentioned, correctly typed, and properly grounded in the transcript.

    ```
    Evaluate the accuracy and grounding of entities extracted by the system.

    For every entity in output.entities, verify:

    1. The entity is actually mentioned in the current conversation.
    2. The entity surface_form exactly appears in the raw transcript.
    3. The entity type is appropriate.
    4. Any canonicalization is supported by the allowed sound-alike/evidence rules.
    5. The evidence_quote is an exact quote from the transcript.
    6. The system has not inferred an entity that was never spoken.
    7. Ambiguous or garbled speech is not incorrectly converted into a confident named entity.

    Do not reward entities simply because they are plausible.

    If the transcript does not provide enough evidence, the correct behavior is to leave the entity unrecognized.

    Return a score from 0 to 1:
    1.0 = All entities are correctly extracted and fully grounded
    0.75 = Minor issue with an entity but no significant hallucination
    0.5 = Some entities are weakly grounded or incorrectly typed
    0.25 = Multiple unsupported entities
    0.0 = Entities are largely hallucinated or incorrectly grounded

    System prompt & user prompt: {{input}}
    Assistant output: {{output}}
    ```

4. `speaker_attribution_accuracy` - Measures whether speakers are correctly attributed and whether the system avoids unsupported speaker guesses.

    ```
    Evaluate whether speaker attribution in the output is correct and grounded in the input transcript.

    Check:
    - Whether <<spk_N>> references correspond to the correct speaker
    - Whether speaker attribution is supported by the dialogue
    - Whether the model incorrectly assigns statements to a speaker
    - Whether speaker guesses are made when there is insufficient evidence
    - Whether speaker attribution is consistent between rewritten_transcript, card summary, entities, and speaker flags

    The system should NOT guess a speaker merely because of weak evidence.

    When speaker identity cannot be established confidently, it is better to leave the speaker unknown than to invent an attribution.

    Return a score from 0 to 1:
    1.0 = All speaker attribution is correct and well supported
    0.75 = Minor attribution uncertainty but no meaningful error
    0.5 = One or more questionable speaker assignments
    0.25 = Significant incorrect attribution
    0.0 = Speaker attribution is largely fabricated or incorrect

    System prompt & user prompt: {{input}}
    Assistant output: {{output}}
    ```

5. `action_intent_preservation` - Measures whether requests, decisions, commitments, questions, and intended actions are preserved without changing their meaning or certainty.

    ```
    Evaluate whether the input_enrichment_rewrite output preserves the user's original intent.

    Identify any:
    - Requests
    - Commands
    - Questions
    - Decisions
    - Commitments
    - Promises
    - Planned actions
    - Explicitly stated outcomes

    Compare the intent expressed in the raw transcript with the rewritten transcript and generated summary.

    The system must NOT:
    - Turn a request into a commitment
    - Turn a possibility into a decision
    - Turn a question into an answer
    - Turn a suggestion into an instruction
    - Invent an action that was never stated
    - Remove an important requested action
    - Change who is expected to perform an action
    - Change the certainty of an action

    If the transcript is ambiguous, the system should preserve the ambiguity rather than invent an intent.

    Return a score from 0 to 1:
    1.0 = Original intent is completely preserved
    0.75 = Minor wording difference but intent remains intact
    0.5 = Noticeable change in intent or certainty
    0.25 = Major change to the intended action
    0.0 = Intent is completely lost or fabricated

    System prompt & user prompt: {{input}}
    Assistant output: {{output}}
    ```