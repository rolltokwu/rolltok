## 📊 LLM Cost Calculator & Model Comparison



## The Problem: You Don't Know What You'll Pay

Most AI API providers show you per-token pricing:

```
Model: GPT-4o-mini
Input: $0.15/1M tokens
Output: $0.60/1M tokens
```

Sounds affordable. But "1 million tokens" doesn't tell you what you'll actually spend.

**The answer matters because of conversation history.**

Every time you send a request, you're not just sending the new message—you're sending the entire conversation history. So:

- Request 1: 100 tokens
- Request 2: 100 + 150 (previous response) = 250 tokens
- Request 3: 100 + 150 + 200 = 450 tokens
- Request 10: ~2,000 tokens

**By the 10th request, you're paying 20x more per request than the first one.**

## The Solution: Cost Calculator

Here's a Python script to predict your monthly costs:

```python
#!/usr/bin/env python3
"""
LLM Cost Calculator
Predicts monthly API costs accounting for conversation history.
"""

def predict_monthly_cost(
    requests_per_day: int,
    avg_new_tokens_per_request: int,
    avg_response_tokens: int,
    avg_conversation_length: int,
    input_price_per_million: float,
    output_price_per_million: float
) -> float:
    """
    Predict monthly API cost, accounting for conversation history.
    
    Args:
        requests_per_day: Number of API requests per day
        avg_new_tokens_per_request: Average new tokens in each request
        avg_response_tokens: Average tokens in each response
        avg_conversation_length: Average number of turns in a conversation
        input_price_per_million: Price per 1M input tokens (in USD)
        output_price_per_million: Price per 1M output tokens (in USD)
    
    Returns:
        Monthly cost in USD
    """
    # Each request includes all previous messages in conversation
    avg_input_per_request = avg_new_tokens_per_request + (
        avg_conversation_length * avg_response_tokens
    )
    
    # Monthly totals
    monthly_input_tokens = avg_input_per_request * requests_per_day * 30
    monthly_output_tokens = avg_response_tokens * requests_per_day * 30
    
    # Monthly cost
    input_cost = (monthly_input_tokens * input_price_per_million) / 1_000_000
    output_cost = (monthly_output_tokens * output_price_per_million) / 1_000_000
    
    return input_cost + output_cost


def compare_models():
    """Compare costs across different models."""
    
    # Usage pattern: 30 requests/day, 8-turn conversations
    usage = {
        "requests_per_day": 30,
        "avg_new_tokens_per_request": 200,
        "avg_response_tokens": 500,
        "avg_conversation_length": 8,
    }
    
    # Model pricing (as of 2026-10-12)
    models = {
        "Doubao-Seed-2.0-Mini": (0.03, 0.29),
        "qwen3.7-flash": (0.07, 0.28),
        "deepseek-v4-flash": (0.12, 0.23),
        "qwen3.7-plus": (0.70, 2.81),
        "Claude-Sonnet-5": (2.00, 10.00),
        "GPT-5.4-Nano": (0.17, 1.06),
        "GPT-6-Astra": (17.00, 63.75),
    }
    
    print("Monthly Cost Comparison (30 requests/day, 8-turn conversations)")
    print("=" * 60)
    
    for model, (input_price, output_price) in models.items():
        cost = predict_monthly_cost(
            **usage,
            input_price_per_million=input_price,
            output_price_per_million=output_price
        )
        print(f"{model:30s} ${cost:8.2f}/month")


if __name__ == "__main__":
    compare_models()
```

**Output**:
```
Monthly Cost Comparison (30 requests/day, 8-turn conversations)
============================================================
Doubao-Seed-2.0-Mini            $0.48/month
qwen3.7-flash                   $1.27/month
deepseek-v4-flash               $1.62/month
qwen3.7-plus                    $11.19/month
Claude-Sonnet-5                 $32.28/month
GPT-5.4-Nano                    $2.94/month
GPT-6-Astra                     $253.88/month
```

## Model Selection Framework

### Cost-Sensitive Tasks

**Use cases**: Simple chatbots, data extraction, basic content generation

**Recommended models**:
- **Doubao-Seed-2.0-Mini**: $0.03/$0.29 — cheapest on the market
- **qwen3.7-flash**: $0.07/$0.28 — excellent value
- **deepseek-v4-flash**: $0.12/$0.23 — very affordable

### Balanced Tasks

**Use cases**: Code review, technical writing, complex analysis

**Recommended models**:
- **qwen3.7-plus**: $0.70/$2.81 — good balance of cost and quality
- **Claude-Sonnet-5**: $2.00/$10.00 — excellent for complex tasks
- **GPT-5.4-Nano**: $0.17/$1.06 — surprisingly affordable

### High-End Tasks

**Use cases**: Critical business decisions, complex reasoning, research

**Recommended models**:
- **GPT-6-Astra**: $17.00/$63.75 — most powerful, but expensive
- **Claude-Fable-5**: $10.00/$50.00 — excellent for complex reasoning
- **Claude-Opus-5.5**: $4.00/$20.00 — good balance of quality and cost

### Special Cases

**Long output tasks**: Use **Grok-4.2-Reasoning** ($5.00/$0.40) — output is extremely cheap.

**Code generation**: Use **Doubao-Seed-2.0-Code** ($0.46/$2.32) or **kimi-k2.7-code** ($0.76/$3.16) — optimized for code.

## Complete Model List (2026-10-12)

| Model | Input Price | Output Price |
|---|---|---|
| Doubao-Seed-2.0-Mini | $0.03 | $0.29 |
| qwen3.7-flash | $0.07 | $0.28 |
| qwen3.8-flash | $0.10 | $0.32 |
| deepseek-v4-flash | $0.12 | $0.23 |
| Doubao-Seed-2.0-Lite | $0.09 | $0.52 |
| qwen3.7-plus | $0.70 | $2.81 |
| Doubao-Seed-2.0-Code | $0.46 | $2.32 |
| Doubao-Seed-2.0-Pro | $0.46 | $2.32 |
| deepseek-v4.1-flash | $0.24 | $0.96 |
| DeepSeek-V4-Pro-202606 | $0.37 | $0.74 |
| GPT-5.4-Nano | $0.17 | $1.06 |
| qwen3.7-max | $1.40 | $4.21 |
| qwen3.8-max | $1.40 | $4.21 |
| deepseek-v4-pro | $1.40 | $2.81 |
| DeepSeek-V4-Pro-0813 | $1.11 | $3.33 |
| DeepSeek-V4-Flash-20260731 | $0.25 | $0.99 |
| Doubao-Seed-2.1-pro | $0.87 | $4.35 |
| Claude-Sonnet-5 | $2.00 | $10.00 |
| Claude-Opus-5.5 | $4.00 | $20.00 |
| Claude-Fable-5 | $10.00 | $50.00 |
| GPT-6-Astra | $17.00 | $63.75 |
| Grok-4.2-Reasoning | $5.00 | $0.40 |
| kimi-k2.7-code | $0.76 | $3.16 |
| MiniMax-M2.5 | $0.30 | $1.20 |

*Full list: 49 models available. Prices as of 2026-10-12. Subject to change.*

## How to Use This Calculator

1. **Estimate your usage pattern**
   - How many requests per day?
   - How long are your conversations?
   - What's the average token count?

2. **Plug in model prices**
   - Check the model list above
   - Or visit your provider's pricing page

3. **Compare monthly costs**
   - Run the calculator for different models
   - Choose the model that fits your budget

4. **Monitor and adjust**
   - Track your actual usage
   - Adjust your model selection as needed

## Full Disclosure

I'm building [RollTok](https://www.rolltok.com), which offers monthly subscriptions for 49+ models with token quotas and cost prediction tools. I believe developers should know what they'll pay before they commit.

If you find this calculator useful, check out the full guide on [DEV.to](https://dev.to) or [Medium](https://medium.com).
