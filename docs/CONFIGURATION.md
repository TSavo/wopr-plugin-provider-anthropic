# Anthropic Claude Configuration

## Required Settings

| Option | Type | Required | Description |
|--------|------|----------|-------------|
| `apiKey` | string | **Yes** | Anthropic API key |

## Optional Settings

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `defaultModel` | string | `"claude-3-opus-20240229"` | Default Claude model |
| `maxTokens` | number | `4096` | Maximum tokens per response |
| `temperature` | number | `0.7` | Sampling temperature |

## Available Models

| Model | Context | Best For |
|-------|---------|----------|
| `claude-3-opus-20240229` | 200K | Complex tasks, coding |
| `claude-3-sonnet-20240229` | 200K | Balanced performance |
| `claude-3-haiku-20240307` | 200K | Fast responses |

## Configuration Example

```json
{
  "apiKey": "sk-ant-api03-...",
  "defaultModel": "claude-3-opus-20240229",
  "maxTokens": 4096,
  "temperature": 0.7
}
```

## Environment Variable

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
```

## Troubleshooting

**"Invalid API key"**
- Get key from https://console.anthropic.com
- Ensure key has billing enabled

**"Rate limit exceeded"**
- Check usage at https://console.anthropic.com
- Implement request throttling
- Consider upgrading tier
