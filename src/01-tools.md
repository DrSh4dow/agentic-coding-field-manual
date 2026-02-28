# 01 - Tools

## Requirements

Your AI coding tool needs three things:

* **Plan mode** - Separate thinking from writing. You align on the approach *before* code is produced.
* **LSP integration** - The model sees type errors, completions, and diagnostics. Same information your editor has.
* **Conversation forking** - Explore an alternative without losing your current thread.

Two tools check all boxes:

1. [OpenCode](https://opencode.ai/) - Open source, any model. My preference.
2. [Claude Code](https://claude.com/product/claude-code) - Anthropic's CLI.

## OpenCode Setup

Install OpenCode, then update `~/.config/opencode/opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "openai/gpt-5.3-codex",
  "tui": {
    "scroll_speed": 3,
    "scroll_acceleration": {
      "enabled": true
    },
    "diff_style": "auto"
  },
  "tools": {
    "lsp": true
  },
  "mcp": {
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp",
      "headers": {
        "CONTEXT7_API_KEY": "{env:CONTEXT7_API_KEY}"
      }
    },
    "playwright": {
      "type": "local",
      "enabled": true,
      "command": [
        "bunx",
        "@playwright/mcp@latest"
      ]
    }
  },
  "lsp": {
    "biome": {
      "command": [
        "biome",
        "lsp-proxy"
      ],
      "extensions": [
        ".js",
        ".jsx",
        ".ts",
        ".tsx",
        "json",
        "jsonc"
      ]
    }
  }
}
```

Add env vars to your `.bashrc`, `.zshrc`, or `config.fish` (if you are one of those guys):

```bash
export OPENCODE_EXPERIMENTAL_LSP_TOOL=true
export CONTEXT7_API_KEY=<your-key-here>
```

Get a Context7 API key at [context7.com](https://context7.com).

On first run, OpenCode prompts for a provider. Pick either OpenAI or Anthropic. I personally recommend Codex-5.3 for coding because he does what I say (heisenberg, BB), and Claude for creative writing or design experimentation because it's like a kid wanting to do everything on the first shot and try creative stuff.

> **What's an LSP?** It's how your IDE understands autocompletion and syntax. The model gets the same type information and diagnostics your editor does - real errors, not hallucinated ones. [Watch this explainer by TJ DeVries](https://www.youtube.com/watch?v=LaS32vctfOY) if unfamiliar.

## MCP Servers

The config above includes two [MCP](https://modelcontextprotocol.io/) servers:

* **Context7** - Documentation lookup. The model queries real, up-to-date library docs instead of hallucinating APIs from training data. This is non-negotiable - without it, the model invents function signatures that don't exist.
* **Playwright** - Browser automation. Takes screenshots of UI changes for visual verification. More on this in the [UI appendix](appendix-ui.md).

You can add more MCP servers as needed. The point is: give the model access to real data sources instead of letting it guess.

> **Keep it lean.** Every MCP server or Skill you add bloats the system prompt and eats into the model's context window. The more you pile on, the dumber the model gets - less room to actually reason about your problem. Only keep what you actively use. If you're not using it, rip it out.
