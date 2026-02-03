# Anthropic Claude Configuration

## Required Settings

| Option | Type | Required | Description |
|--------|------|----------|-------------|
| `apiKey` | string | **Yes** | Anthropic API key (starts with `sk-ant-`) |

## Query Options

All options available when calling `query()`:

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `prompt` | string | **Required** | The prompt to send to Claude |
| `systemPrompt` | string | - | System prompt to set context |
| `model` | string | `"claude-opus-4-5-20251101"` | Model to use |
| `maxTokens` | number | `4096` | Maximum tokens per response |
| `temperature` | number | - | Sampling temperature (0-1) |
| `topP` | number | - | Top-p sampling |
| `resume` | string | - | Session ID to resume |
| `images` | string[] | - | Image URLs to include (vision) |
| `tools` | string[] | - | Built-in tools to enable |
| `a2aServers` | object | - | A2A MCP server configurations |
| `allowedTools` | string[] | - | Tools auto-allowed without prompts |
| `providerOptions` | object | - | Additional provider-specific options |

## Available Models

| Model | ID | Best For |
|-------|-----|----------|
| Claude Opus 4.5 | `claude-opus-4-5-20251101` | Complex tasks, deep analysis, coding |
| Claude Sonnet 4 | `claude-sonnet-4-20250514` | Balanced performance and cost |
| Claude Haiku 4.5 | `claude-haiku-4-5-20251001` | Fast responses, simple tasks |

## Configuration Example

### Basic Usage

```typescript
{
  prompt: "Hello, Claude!",
  model: "claude-opus-4-5-20251101",
  maxTokens: 4096
}
```

### With System Prompt

```typescript
{
  prompt: "Explain this code",
  systemPrompt: "You are a helpful coding assistant.",
  model: "claude-sonnet-4-20250514"
}
```

### Session Resumption

```typescript
{
  prompt: "Continue our previous discussion",
  resume: "session-abc123-xyz789"
}
```

The session ID is returned in the stream as a `system` message with `subtype: "init"`:

```typescript
for await (const msg of client.query(options)) {
  if (msg.type === 'system' && msg.subtype === 'init') {
    console.log('Session ID:', msg.session_id);
  }
}
```

### Vision (Images)

```typescript
{
  prompt: "What's in this image?",
  images: [
    "https://example.com/image1.png",
    "https://example.com/image2.jpg"
  ]
}
```

Images are automatically downloaded and converted to base64. Supported formats include JPEG, PNG, GIF, and WebP.

### Built-in Tools

Enable Claude's built-in tools for file operations and command execution:

```typescript
{
  prompt: "Read the package.json and list all dependencies",
  tools: ["Read", "Edit", "Bash", "Glob", "Grep"]
}
```

Available built-in tools:
- `Read` - Read file contents
- `Edit` - Edit files
- `Bash` - Execute shell commands
- `Glob` - Find files by pattern
- `Grep` - Search file contents

### A2A Tools (Agent-to-Agent)

Register custom tools via MCP servers for agent-to-agent communication:

```typescript
{
  prompt: "Use the calculator to add 5 and 3",
  a2aServers: {
    "calculator": {
      name: "Calculator Server",
      version: "1.0.0",
      tools: [{
        name: "add",
        description: "Add two numbers",
        inputSchema: {
          type: "object",
          properties: {
            a: { type: "number", description: "First number" },
            b: { type: "number", description: "Second number" }
          },
          required: ["a", "b"]
        },
        handler: async (args) => ({
          content: [{
            type: "text",
            text: String(args.a + args.b)
          }]
        })
      }]
    }
  },
  allowedTools: ["mcp__calculator__add"]
}
```

A2A tool format for `allowedTools`: `mcp__<server-name>__<tool-name>`

### A2A Tool Result Format

Tool handlers must return results in this format:

```typescript
{
  content: Array<{
    type: "text" | "image" | "resource";
    text?: string;      // For type: "text"
    data?: string;      // For type: "image" (base64)
    mimeType?: string;  // For type: "image"
  }>;
  isError?: boolean;    // Set to true if an error occurred
}
```

## Environment Variable

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
```

The plugin will automatically use this environment variable if set.

## UI Config Schema

The plugin registers this configuration schema for UI integration:

| Field | Type | Label | Default | Description |
|-------|------|-------|---------|-------------|
| `apiKey` | password | API Key | - | Anthropic API key |
| `model` | select | Default Model | `claude-opus-4-5-20251101` | Default model for sessions |
| `maxTokens` | number | Max Tokens | `4096` | Maximum tokens per response |

## Permission Mode (yoloMode)

The plugin operates in bypass mode by default, automatically approving all tool operations:

```typescript
permissionMode: "bypassPermissions",
allowDangerouslySkipPermissions: true
```

This is equivalent to "yoloMode" in other providers, allowing automated workflows without manual approval.

## Troubleshooting

**"Invalid API key"**
- Ensure your key starts with `sk-ant-`
- Get a key from https://console.anthropic.com
- Verify billing is enabled on your account

**"Rate limit exceeded"**
- Check usage at https://console.anthropic.com
- Implement request throttling in your application
- Consider upgrading your API tier

**"Session not found"**
- Session IDs expire after a period of inactivity
- Store session IDs and handle expiration gracefully

**"Image download failed"**
- Verify the image URL is accessible
- Check that the URL returns a valid image content-type
- Ensure the image is not behind authentication

**"Tool not found"**
- For built-in tools, verify the tool name is correct (case-sensitive)
- For A2A tools, use the format `mcp__<server>__<tool>`
- Ensure the tool is listed in `allowedTools` if using auto-approval
