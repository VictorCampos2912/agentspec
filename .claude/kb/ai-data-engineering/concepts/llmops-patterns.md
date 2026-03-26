# LLMOps Patterns

> **Purpose**: Prompt versioning, token economics, guardrails, evaluation frameworks, observability
> **Confidence**: 0.85
> **MCP Validated**: 2026-03-26

## Overview

LLMOps applies MLOps principles to large language model deployments: version prompts like code, track token costs as a first-class metric, enforce guardrails on both input and output, evaluate quality systematically, and observe production behavior with tracing. The discipline is maturing rapidly with tools like LangSmith, Arize Phoenix, and Braintrust replacing ad-hoc logging.

## The Concept

```python
# LLMOps: Prompt versioning + guardrails + cost tracking
from dataclasses import dataclass, field
from datetime import datetime
import tiktoken

@dataclass
class VersionedPrompt:
    """Prompt with version tracking, cost estimation, and guardrails."""
    name: str
    version: str
    template: str
    model: str = "claude-sonnet-4-20250514"
    max_input_tokens: int = 4096
    max_output_tokens: int = 1024
    created_at: str = field(default_factory=lambda: datetime.utcnow().isoformat())

    # Cost per million tokens (input / output)
    COST_TABLE = {
        "claude-sonnet-4-20250514": (3.00, 15.00),
        "gpt-4o": (2.50, 10.00),
        "gpt-4o-mini": (0.15, 0.60),
        "claude-3-5-haiku-20241022": (0.80, 4.00),
    }

    def estimate_cost(self, input_tokens: int, output_tokens: int) -> float:
        """Estimate cost in USD for a single call."""
        input_rate, output_rate = self.COST_TABLE.get(self.model, (0, 0))
        return (input_tokens * input_rate + output_tokens * output_rate) / 1_000_000

    def validate_input(self, user_input: str) -> tuple[bool, str]:
        """Input guardrails: length, injection, PII patterns."""
        enc = tiktoken.encoding_for_model("gpt-4o")
        tokens = len(enc.encode(user_input))
        if tokens > self.max_input_tokens:
            return False, f"Input too long: {tokens} > {self.max_input_tokens}"
        injection_patterns = ["ignore previous", "system:", "you are now"]
        for pattern in injection_patterns:
            if pattern.lower() in user_input.lower():
                return False, f"Potential prompt injection: '{pattern}'"
        return True, "OK"

    def validate_output(self, output: str) -> tuple[bool, str]:
        """Output guardrails: refusal, hallucination markers."""
        refusal_markers = ["I cannot", "I'm unable", "as an AI"]
        if any(marker.lower() in output.lower() for marker in refusal_markers):
            return False, "Model refusal detected"
        return True, "OK"
```

## Quick Reference

| LLMOps Area | Tools | Key Metric |
|-------------|-------|-----------|
| Prompt versioning | Git, LangSmith Hub, Humanloop | Prompt version -> quality delta |
| Token economics | tiktoken, LiteLLM, custom | Cost per query, cost per user |
| Input guardrails | Guardrails AI, NeMo, custom | Blocked injection rate |
| Output guardrails | Guardrails AI, LMQL, custom | Hallucination rate, refusal rate |
| Evaluation | RAGAS, DeepEval, Braintrust | Faithfulness, relevancy, F1 |
| Observability | LangSmith, Arize Phoenix, Langfuse | Latency p99, error rate, drift |
| A/B testing | Braintrust, custom | Win rate, preference score |

| Eval Metric (RAG) | Measures | Target |
|--------------------|----------|--------|
| Faithfulness | Answer grounded in context | > 0.90 |
| Answer relevancy | Answer addresses the question | > 0.85 |
| Context precision | Retrieved docs are relevant | > 0.80 |
| Context recall | All needed info was retrieved | > 0.80 |

## Common Mistakes

### Wrong

```python
# Hardcoded prompts with no versioning or cost tracking
response = llm.chat("You are a helpful assistant. Answer: " + user_query)
# No version, no cost tracking, no guardrails, no eval
```

### Correct

```python
# Versioned prompt with full observability
prompt = VersionedPrompt(
    name="rag-answer",
    version="2.1.0",
    template="Answer based on context:\n{context}\n\nQuestion: {question}",
    model="claude-sonnet-4-20250514",
)

valid, msg = prompt.validate_input(user_query)
if not valid:
    log.warning(f"Input rejected: {msg}")
    return fallback_response()

response = llm.chat(prompt.template.format(context=ctx, question=user_query))

valid, msg = prompt.validate_output(response)
cost = prompt.estimate_cost(input_tokens=850, output_tokens=200)
log.info(f"prompt={prompt.name} v={prompt.version} cost=${cost:.4f}")
```

## Related

- [rag-pipelines](../concepts/rag-pipelines.md)
- [feature-stores](../concepts/feature-stores.md)
- [rag-pipeline-implementation](../patterns/rag-pipeline-implementation.md)
- [text-to-sql](../patterns/text-to-sql.md)
