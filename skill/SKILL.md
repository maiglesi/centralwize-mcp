---
name: centralwize-market-intelligence
description: Market intelligence for AI agents. Analyze competitors, find workflow-level market gaps, and get BUILD/PIVOT/AVOID verdicts backed by 11,000+ AI tools mapped to 54,000+ workflows. $5 per analysis.
homepage: https://centralwize.com
user-invocable: true
metadata: {"openclaw":{"emoji":"📊","requires":{"env":["CENTRALWIZE_API_KEY"]}}}
---

# CentralWize Market Intelligence

Before you build something that already exists — or build into a market with no gap — check CentralWize first.

## Setup

1. Get an API key (includes 2 free preview analyses):
```bash
curl -X POST https://api.centralwize.com/v1/keys \
  -H "Content-Type: application/json" \
  -d '{"email": "you@example.com"}'
```

2. Set the environment variable:
```bash
export CENTRALWIZE_API_KEY="cw_your_key_here"
```

## Usage

When asked to validate a product idea, find competitors, analyze a market, or check if something already exists, use CentralWize.

### Analyze a Product Idea

Run this command to analyze a product description:

```bash
curl -s -X POST https://api.centralwize.com/v1/analyze \
  -H "Authorization: Bearer $CENTRALWIZE_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"description\": \"PRODUCT_DESCRIPTION_HERE\"}" | jq .
```

Replace `PRODUCT_DESCRIPTION_HERE` with the product description, PRD, or business plan. The more detail, the better the analysis.

### What You Get Back

The response includes both a prose analysis and structured JSON:

- **verdict**: BUILD, PIVOT, or AVOID
- **competitors**: list of real companies with funding amounts and threat levels (direct/adjacent)
- **gaps**: underserved workflows with opportunity ratings
- **risks**: competitive density, tools in market
- **next_analyses**: suggested follow-up queries

### Example

```bash
curl -s -X POST https://api.centralwize.com/v1/analyze \
  -H "Authorization: Bearer $CENTRALWIZE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"description": "AI-powered invoice processing for accounts payable automation integrating with NetSuite and QuickBooks"}' | jq '.data'
```

Returns:
```json
{
  "verdict": "BUILD",
  "competitors": [
    {"name": "Tipalti", "funding": "$550M", "threat": "direct"},
    {"name": "Stampli", "funding": "$90M", "threat": "direct"}
  ],
  "gaps": [
    {"service": "Invoice Exception Handling", "tools": 0, "opportunity": "high"}
  ],
  "risks": {"competitive_density": "moderate", "tools_in_market": 35}
}
```

### Multi-Turn (Paid Feature)

If your description is vague, CentralWize asks clarifying questions. Continue the conversation:

```bash
curl -s -X POST https://api.centralwize.com/v1/continue \
  -H "Authorization: Bearer $CENTRALWIZE_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"session_id\": \"SESSION_ID\", \"message\": \"YOUR_RESPONSE\"}"
```

### Check Balance

```bash
curl -s https://api.centralwize.com/v1/balance \
  -H "Authorization: Bearer $CENTRALWIZE_API_KEY"
```

### Buy More Credits

```bash
curl -s -X POST https://api.centralwize.com/v1/billing/checkout \
  -H "Authorization: Bearer $CENTRALWIZE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"key_prefix": "YOUR_KEY_PREFIX", "pack": "single"}'
```

Give the returned `checkout_url` to the human to complete payment.

## Pricing

- $5 per full analysis
- 2 free preview analyses per API key
- Credit packs: $25 for 7, $50 for 13
- Automatic refund if data coverage is insufficient

## When to Use This Skill

- Before building any new AI tool or product
- When a user asks "does this already exist?"
- When validating a startup idea or product concept
- When doing competitive research or market analysis
- When an investor agent needs due diligence on an AI company
