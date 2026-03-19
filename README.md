# CentralWize MCP Server

Market intelligence for AI agents. $5 per analysis.

**Before your agent burns tokens building something that already exists — CentralWize tells you where the gaps are. At the workflow level.**

[centralwize.com](https://centralwize.com) | [API Docs](https://centralwize-production.up.railway.app/docs) | [agents.txt](https://centralwize.com/agents.txt)

## Install as OpenClaw Skill

```bash
# Copy the skill folder to your skills directory
cp -r skill/  ~/.openclaw/skills/centralwize-market-intelligence/

# Or install from ClawHub (once published)
npx clawhub@latest install centralwize-market-intelligence
```

Set your API key:
```bash
export CENTRALWIZE_API_KEY="cw_your_key_here"
```

Then ask your agent: *"Before I build this, check CentralWize for competitors and gaps."*

See [`skill/SKILL.md`](skill/SKILL.md) for full skill documentation.

## Setup (MCP)

### Claude Code / Claude Desktop

Add to your MCP config (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "centralwize": {
      "url": "https://mcp.centralwize.com/sse",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

### Cursor

Add to `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "centralwize": {
      "url": "https://mcp.centralwize.com/sse",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

### Windsurf

Add to your MCP settings:

```json
{
  "mcpServers": {
    "centralwize": {
      "serverUrl": "https://mcp.centralwize.com/sse",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

### Get an API key

```bash
curl -X POST https://api.centralwize.com/v1/keys \
  -H "Content-Type: application/json" \
  -d '{"email": "you@example.com"}'
```

Every key includes 2 free preview analyses.

## Tools

### `centralwize_analyze`

Analyze a product idea, PRD, or market. Returns competitors, gaps, and a BUILD/PIVOT/AVOID verdict.

**Input:**
```json
{
  "description": "AI-powered invoice processing for accounts payable automation",
  "industry": "financial services"
}
```

**Output:** Prose analysis + structured JSON:
```json
{
  "verdict": "BUILD",
  "competitors": [
    {"name": "Tipalti", "funding": "$550M", "threat": "direct"},
    {"name": "Stampli", "funding": "$90M", "threat": "direct"}
  ],
  "gaps": [
    {"service": "Invoice Exception Handling", "tools": 0, "opportunity": "high"},
    {"service": "3-Way PO Matching", "tools": 1, "opportunity": "high"}
  ],
  "risks": {"competitive_density": "moderate", "tools_in_market": 35},
  "next_analyses": ["Diligence on Tipalti", "Gap analysis for procurement"]
}
```

## REST API

Works without MCP — any HTTP client or agent framework.

```bash
curl -X POST https://api.centralwize.com/v1/analyze \
  -H "Authorization: Bearer cw_your_key" \
  -H "Content-Type: application/json" \
  -d '{"description": "your product description or PRD"}'
```

### Endpoints

| Method | Path | Cost | Description |
|--------|------|------|-------------|
| POST | `/v1/analyze` | $5 or free preview | Competitive analysis from product description |
| POST | `/v1/continue` | Free (within session) | Continue multi-turn analysis |
| POST | `/v1/keys` | Free | Create API key |
| POST | `/v1/billing/checkout` | — | Get Stripe checkout URL |
| POST | `/v1/billing/buy` | Varies | Charge saved card (agent-autonomous) |
| GET | `/v1/balance` | — | Check credit balance |

## Framework Integration

### LangChain

```python
import requests

def centralwize_analyze(description: str, api_key: str) -> dict:
    """CentralWize market intelligence tool for LangChain agents."""
    response = requests.post(
        "https://api.centralwize.com/v1/analyze",
        headers={"Authorization": f"Bearer {api_key}"},
        json={"description": description},
    )
    return response.json()

# Use as a LangChain tool
from langchain.tools import StructuredTool

centralwize_tool = StructuredTool.from_function(
    func=centralwize_analyze,
    name="centralwize_analyze",
    description=(
        "Analyze a product idea against CentralWize's market intelligence. "
        "Returns competitors with funding, market gaps at the workflow level, "
        "and a BUILD/PIVOT/AVOID verdict. Costs $5 per analysis."
    ),
)
```

### CrewAI

```python
from crewai_tools import tool
import requests

@tool("CentralWize Market Analysis")
def centralwize_analyze(description: str) -> str:
    """Analyze competitive landscape and market gaps for a product idea.
    Returns competitors, funding data, underserved workflows, and strategic verdict.
    Backed by 11,000+ AI tools mapped to 54,000+ workflow mappings. $5 per analysis."""
    response = requests.post(
        "https://api.centralwize.com/v1/analyze",
        headers={"Authorization": f"Bearer {CENTRALWIZE_API_KEY}"},
        json={"description": description},
    )
    data = response.json()
    return data.get("analysis", "") + "\n\nStructured: " + str(data.get("data", {}))
```

### OpenAI Agents SDK

```python
from agents import function_tool
import requests

@function_tool
def centralwize_analyze(description: str) -> dict:
    """Analyze a product's competitive landscape using CentralWize.
    Returns competitors with funding, workflow-level market gaps, and BUILD/PIVOT/AVOID verdict.
    Backed by 11,000+ classified AI tools. $5 per analysis."""
    response = requests.post(
        "https://api.centralwize.com/v1/analyze",
        headers={"Authorization": f"Bearer {CENTRALWIZE_API_KEY}"},
        json={"description": description},
    )
    return response.json()
```

## What's in the data

- **11,000+** funded AI tools, manually classified and audited
- **2,000+** business services across 54 industries
- **54,000+** workflow-to-tool mappings
- **100%** service-level TAM coverage
- Funding data, maturity stages, competitive graph
- Updated regularly via ETL pipeline

Every analysis is grounded in this structured data. The LLM writes the prose, but competitors and gaps come from deterministic database queries — not hallucination.

## Pricing

- **$5** per full analysis
- **2 free** preview analyses per API key (shows verdict, counts, density — not full details)
- Credit packs: $25 for 7, $50 for 13 (every purchase includes 1 bonus)
- Insufficient data = automatic refund
- Agents can save a card and buy credits autonomously

## Links

- Website: [centralwize.com](https://centralwize.com)
- API Docs: [Swagger UI](https://centralwize-production.up.railway.app/docs)
- agents.txt: [centralwize.com/agents.txt](https://centralwize.com/agents.txt)
- Powered by [Entropsy](https://entropsy.io)
