# RollTok Quick Start

> A minimal, working example of integrating multiple LLM providers through a unified OpenAI-compatible API.

## What This Is

This repository contains a bare-bones Python script that demonstrates how to:
- Switch between different LLM providers (Qwen, DeepSeek, etc.) using a single endpoint
- Handle API errors gracefully
- Stream responses in real-time

**No fluff. No production-ready wrappers. Just the core logic.**

## Prerequisites

- Python 3.9+
- A RollTok API key ([get one here](https://rolltok.com))
- Basic familiarity with HTTP requests

## Quick Start

### 1. Clone and Install

```bash
git clone https://github.com/rolltok/quickstart.git
cd quickstart
pip install requests
```

### 2. Set Your API Key

**Never hardcode your API key in source code.** Use environment variables:

```bash
export ROLLTOK_API_KEY="your-api-key-here"
```

Or create a `.env` file (and add it to `.gitignore`):

```
ROLLTOK_API_KEY=your-api-key-here
```

### 3. Run the Example

```bash
python main.py
```

You should see output like:

```
Sending request to qwen3.7-plus...
Response: Hello! How can I help you today?
```

## How It Works

The script does three things:

1. **Loads your API key** from environment variables
2. **Makes a POST request** to `https://api.rolltok.com/v1/chat/completions`
3. **Parses the JSON response** and extracts the message content

That's it. No magic. No SDK overhead.

### The Core Logic

```python
import os
import requests

api_key = os.getenv("ROLLTOK_API_KEY")

response = requests.post(
    "https://api.rolltok.com/v1/chat/completions",
    headers={
        "Authorization": f"Bearer {api_key}",
        "Content-Type": "application/json"
    },
    json={
        "model": "qwen3.7-plus",
        "messages": [
            {"role": "user", "content": "Hello!"}
        ]
    }
)

print(response.json()["choices"][0]["message"]["content"])
```

## Switching Models

Want to try a different model? Just change the `model` field:

```python
json={
    "model": "deepseek-chat",  # or "qwen3.7-plus", "deepseek-reasoner", etc.
    "messages": [...]
}
```

No code changes. No new SDK. Just swap the model name.

## Error Handling

APIs fail. Here's how to handle common errors:

```python
try:
    response = requests.post(...)
    response.raise_for_status()  # Raise exception for 4xx/5xx
except requests.exceptions.HTTPError as e:
    if response.status_code == 401:
        print("Invalid API key. Check your ROLLTOK_API_KEY.")
    elif response.status_code == 429:
        print("Rate limit exceeded. Wait a bit and retry.")
    else:
        print(f"HTTP error: {e}")
except requests.exceptions.RequestException as e:
    print(f"Request failed: {e}")
```

## Streaming Responses

For real-time output, use the `stream=True` parameter:

```python
response = requests.post(
    "https://api.rolltok.com/v1/chat/completions",
    headers={...},
    json={
        "model": "qwen3.7-plus",
        "messages": [...],
        "stream": True
    },
    stream=True
)

for line in response.iter_lines():
    if line:
        # Parse SSE format: data: {"choices": [{"delta": {"content": "..."}}]}
        print(line.decode("utf-8"))
```

## What's Next?

This is the absolute minimum. For production use, you'll want:
- Retry logic with exponential backoff
- Token usage tracking
- Request/response logging
- Connection pooling

But that's beyond the scope of this quickstart. Start here, then build up.

## Troubleshooting

### "Invalid API key"

- Check that `ROLLTOK_API_KEY` is set correctly
- Make sure there are no extra spaces or quotes
- Verify your key at [rolltok.com/dashboard](https://rolltok.com/dashboard)

### "Rate limit exceeded"

- You're sending too many requests too quickly
- Wait 60 seconds and retry
- Consider implementing exponential backoff

### "Model not found"

- Check the model name spelling
- Verify the model is available at [rolltok.com/docs](https://rolltok.com/docs)

## License

MIT

## Contributing

Found a bug? Have a suggestion? Open an issue or submit a PR.

## Support

- Documentation: [rolltok.com/docs](https://rolltok.com/docs)
- Issues: [GitHub Issues](https://github.com/rolltok/quickstart/issues)
- Email: support@rolltok.com

---

**Full disclosure**: I'm the developer of RollTok. This quickstart is meant to help you get started quickly. If you run into issues, please let me know.
