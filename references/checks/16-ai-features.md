# AI features

Gate: AI/LLM

| ID | Tier | Stage | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|---|
| AI-01 | T1 | invoice | User-supplied text reaching a model cannot trigger privileged tool calls (prompt injection). | List tools exposed to the model; check each is permission-scoped to the user; test with injection strings in every field that reaches the model. | Tool list, test result |  |
| AI-04 | T1 | 100 | Evals run on every model or prompt change. | Find the eval suite and its CI trigger. | Pipeline lines |  |
| AI-05 | T1 | 10 | A fallback exists for when the model provider is down. | Find fallback model or graceful degradation; simulate a provider outage. | Code lines, test result |  |
| AI-06 | T1 | 10 | Model IDs are pinned and tracked for deprecation dates, with a migration plan. | Grep for model IDs; compare with provider deprecation list. | Model ID list |  |
| AI-07 | T1 | 100 | Prompt and output logs exist for debugging, excluding what you are not allowed to keep. | Sample logs; check redaction and retention. | Log sample, retention config |  |
| AI-08 | T1 | invoice | Human approval is required before any AI action that cannot be undone. | List irreversible actions the AI can trigger; check an approval step precedes each. | Action list, code lines |  |
