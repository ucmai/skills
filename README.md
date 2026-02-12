# UCM Skill — API Marketplace for AI Agents

An [Agent Skills](https://agentskills.io) compatible skill that gives any AI agent instant access to 50+ API endpoints — web search, image generation, code execution, text-to-speech, email, stock data, and more.

**Works on:** Claude Code, OpenAI Codex CLI, GitHub Copilot, Cursor, VS Code, Gemini CLI, Goose, Roo Code, Windsurf, Amp, and any platform supporting the Agent Skills standard.

## What It Does

When this skill is installed, your agent can:

1. **Discover services** — Search by natural language ("I need to convert a PDF to text")
2. **Call services** — One HTTP call to purchase and execute any service
3. **Get $1.00 free credits** — Register automatically, enough for 100+ API calls

No SDK, no MCP server, no running processes — just HTTP calls.

## Install

### Claude Code (Plugin Marketplace)

```bash
# Add the UCM marketplace
/plugin marketplace add ucmai/ucm.ai

# Install the skill
/plugin install ucm@ucm-marketplace
```

### Claude Code (Manual)

```bash
# Personal (available in all projects)
cp -r ucm ~/.claude/skills/ucm

# Project (available to anyone who clones the repo)
cp -r ucm .claude/skills/ucm
```

### OpenAI Codex CLI

```bash
cp -r ucm ~/.agents/skills/ucm
```

### GitHub Copilot / VS Code

```bash
# Project level
cp -r ucm .github/skills/ucm

# Or personal level
cp -r ucm ~/.copilot/skills/ucm
```

### Cursor

```bash
cp -r ucm .cursor/skills/ucm
```

### Any Agent (Generic)

Copy the `ucm/` directory into your agent's skills directory, or simply read and follow the instructions in `ucm/SKILL.md`.

## Directory Structure

```
skills/
├── README.md                     # This file
├── .claude-plugin/
│   ├── marketplace.json          # Plugin marketplace manifest
│   └── plugin.json               # Plugin metadata
└── ucm/
    ├── SKILL.md                  # Main skill (Agent Skills standard)
    ├── scripts/
    │   └── register.sh           # Auto-registration script
    └── references/
        └── service-catalog.md    # Full service catalog (52 endpoints)
```

## Quick Test

After installing, ask your agent:

> "Search the web for the latest AI news using UCM"

The agent should automatically:
1. Check if it has a UCM API key
2. Register if needed (free, $1.00 credits)
3. Call `ucm/web-search` via `POST /v1/call`
4. Return the search results

## Available Services

| Service | Price | What It Does |
|---------|-------|--------------|
| ucm/web-search | $0.01 | Real-time web search |
| ucm/web-scrape | $0.02 | Extract webpage content as markdown |
| ucm/image-generation | $0.05 | Generate images from text prompts |
| ucm/code-sandbox | $0.03 | Execute code in isolated sandbox |
| ucm/text-to-speech | $0.01 | Convert text to spoken audio |
| ucm/speech-to-text | $0.01 | Transcribe audio to text |
| ucm/email | $0.01 | Send emails (with recipient verification) |
| ucm/doc-convert | $0.02 | Convert PDF/DOCX/XLSX to markdown |
| ucm/us-stock | $0.01 | US stock quotes, financials, news |
| ucm/cn-finance | $0.01 | China A-share data, macro indicators |
| ucm/weather | FREE | Weather, forecast, air quality |

## Links

- **Website:** https://ucm.ai
- **Dashboard:** https://dashboard.ucm.ai
- **API Docs:** https://ucm.ai/docs
- **GitHub:** https://github.com/ucmai/ucm.ai
- **Agent Skills Standard:** https://agentskills.io
