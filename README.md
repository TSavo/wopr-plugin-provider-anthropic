# wopr-plugin-provider-anthropic

[![npm version](https://img.shields.io/npm/v/wopr-plugin-provider-anthropic.svg)](https://www.npmjs.com/package/wopr-plugin-provider-anthropic)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![WOPR](https://img.shields.io/badge/WOPR-Plugin-blue)](https://github.com/TSavo/wopr)

Anthropic Claude provider plugin for [WOPR](https://github.com/TSavo/wopr) using the Claude Agent SDK.

> Part of the [WOPR](https://github.com/TSavo/wopr) ecosystem - Self-sovereign AI session management over P2P.

## Features

- **Session Resumption** - Resume previous sessions using session ID
- **Vision Support** - Process images via URL with automatic base64 conversion
- **A2A Tools** - Agent-to-Agent tool integration via MCP servers
- **Built-in Tools** - Access to Claude's built-in tools (Read, Edit, Bash, Glob, Grep)
- **yoloMode** - Bypass permissions for automated workflows (`permissionMode: bypassPermissions`)
- **Winston Logging** - Structured logging for debugging

## Installation

```bash
wopr plugin install wopr-plugin-provider-anthropic
```

## Configuration

Add your Anthropic API key:

```bash
wopr providers add anthropic sk-ant-...
```

Or set via environment variable:

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
```

## Usage

Create a session with Anthropic provider:

```bash
wopr session create my-session --provider anthropic
```

Or set provider on existing session:

```bash
wopr session set-provider my-session anthropic
```

### Session Resumption

Resume a previous session:

```typescript
const options = {
  prompt: "Continue where we left off",
  resume: "session-id-from-previous-session"
};
```

### Vision Support

Include images in your prompts:

```typescript
const options = {
  prompt: "What's in this image?",
  images: ["https://example.com/image.png"]
};
```

### Built-in Tools

Enable Claude's built-in tools:

```typescript
const options = {
  prompt: "Read the file and fix the bug",
  tools: ["Read", "Edit", "Bash", "Glob", "Grep"]
};
```

### A2A Tools (Agent-to-Agent)

Register custom tools via MCP servers:

```typescript
const options = {
  prompt: "Use my custom tool",
  a2aServers: {
    "my-server": {
      name: "My A2A Server",
      version: "1.0.0",
      tools: [{
        name: "my_tool",
        description: "Does something useful",
        inputSchema: {
          type: "object",
          properties: {
            input: { type: "string" }
          }
        },
        handler: async (args) => ({
          content: [{ type: "text", text: `Result: ${args.input}` }]
        })
      }]
    }
  },
  allowedTools: ["mcp__my-server__my_tool"]
};
```

## Supported Models

| Model | ID | Best For |
|-------|-----|----------|
| Claude Opus 4.5 | `claude-opus-4-5-20251101` (default) | Complex tasks, coding, analysis |
| Claude Sonnet 4 | `claude-sonnet-4-20250514` | Balanced performance |
| Claude Haiku 4.5 | `claude-haiku-4-5-20251001` | Fast responses |

## API Reference

See [docs/CONFIGURATION.md](docs/CONFIGURATION.md) for full configuration options.

## Development

```bash
npm install
npm run build
```

## Dependencies

- `@anthropic-ai/claude-agent-sdk` - Claude Agent SDK
- `winston` - Logging
- `zod` - Schema validation

## License

MIT
