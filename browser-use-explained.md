# Browser-Use: A Developer Overview

## What?

Browser-Use is an open-source Python library that enables AI agents to autonomously browse and interact with websites.

```python
from browser_use import Agent, Browser, ChatBrowserUse

agent = Agent(
    task="Find the number of stars on our GitHub repo",
    llm=ChatBrowserUse(),
    browser=Browser()
)
await agent.run()
```

**Core Feature:** The intelligent loop that:
1. Takes a natural language task
2. Uses an LLM to decide actions
3. Executes via CDP (Chrome DevTools Protocol)
4. Repeats until completion

---

## How Browser Control Works

### CDP = Browser's Remote Control API

Chrome exposes its functionality through CDP (Chrome DevTools Protocol):

| CDP Command | Action |
|-------------|--------|
| `Page.navigate` | Go to URL |
| `Input.dispatchKeyEvent` | Type/press keys |
| `DOM.click` | Click elements |
| `Runtime.evaluate` | Execute JavaScript |

```python
# What happens when agent says "click button 5"
await page.click(backend_node_id=12345)
# Translates to CDP: DOM.resolveNode → Input.dispatchMouseEvent
```

**Remote Control Concept:** CDP is process-to-process. Your Python code runs in one process, Chrome in another, connected via WebSocket.

---

## Agent Perception: What the Agent Sees

### Dual Input System

| Input | Purpose |
|-------|---------|
| **Screenshot** | Visual layout, images, colors |
| **Interactive Elements** | Structured list of clickable items |

### Interactive Elements Format

```
Interactive Elements:
[1]<button aria-label="Submit">Submit</button>
[2]<input type="text" placeholder="Username">
[3]<a href="/about">About</a>
```

### What DOM Includes

✅ **INCLUDED:**
- Interactive elements (buttons, inputs, links)
- Accessibility tree (ARIA labels, roles)
- Text content, element indices
- Viewport position hints

❌ **EXCLUDED:**
- `<script>`, `<style>`, `<head>`
- Decorative SVG elements
- Non-interactive divs

---

## Browser-Use vs Traditional Scraping

| Aspect | Traditional | Browser-Use |
|--------|-------------|-------------|
| Selectors | Hard-coded CSS/XPath | Agent finds interactives |
| Workflow | Pre-defined steps | Agent decides on the fly |
| Pagination | Loop through pages | Agent scrolls/clicks as needed |
| Dynamic content | Often breaks | Agent adapts |

### Traditional Scraping

```python
# Must define everything upfront
for page in pages:
    scroll()
    click_load_more()
    data = response.css('.product-price::text')
```

### Browser-Use

```python
task = "Get all product prices and dates"
# Agent decides: scroll → click "Load More" → extract
```

---

## Hard-to-Scrape Sites (Douyin, TikTok, etc.)

### Browser-Use Advantages

| Challenge | Browser-Use Solution |
|-----------|---------------------|
| IP blocking | Cloud browsers + proxy rotation |
| Fingerprinting | Stealth browser profiles |
| Dynamic content | Real browser execution |

### Limitations

- ❌ Cannot bypass paywalls
- ❌ CAPTCHAs not solved automatically
- ⚠️ Anti-bot detection still applies

```python
# Best chance for difficult sites
browser = Browser(
    use_cloud=True,
    cloud_proxy_country_code='us',
)
```

---

## Beyond Browser: Current State (2025)

### Desktop Automation

| Tool | Status | Description |
|------|--------|-------------|
| **Claude Computer Use** | Production | Anthropic's computer control API |
| **Microsoft Copilot Studio** | Production | Automate web + desktop apps |

### Mobile Automation

| Project | Status | Description |
|---------|--------|-------------|
| **Mobile-Agent-E** | Research (2025) | Self-evolving Android assistant |
| **AppCopilot** | Research (Sep 2025) | General mobile agent framework |
| **DigiData** | Research (Nov 2025) | Training general-purpose mobile agents |

---

## The Future of App Interfaces

### Two Competing Visions

#### 1. Agent-First Design
- New apps redesign interfaces specifically for AI
- Machine-readable metadata, structured data
- Challenges: Legacy apps won't redesign

#### 2. Unified Interface Layer (MCP)
- Protocol that unifies all app interactions
- Works with ALL existing apps (no redesign needed)

### Why MCP Wins First

| Factor | MCP Advantage |
|--------|---------------|
| **Backward compatibility** | Works with existing apps |
| **No redesign needed** | Apps don't need to change |
| **Industry momentum** | Already being adopted (2025) |
| **Speed** | Deploy NOW |

### Both Will Coexist

```
2025          2027          2029          2030+
  │             │              │              │
  ├─ MCP       ├─ MCP         ├─ MCP         ├─ MCP
  │  dominates │  standard     │  mature      │  everywhere
  │            │               │              │
  │            ├─ Agent-First  ├─ Agent-First ├─ Agent-First
  │            │  (new apps)   │  (growing)   │  (mainstream)
```

---

## MCP (Model Context Protocol)

### What is MCP?

> "MCP is the 'universal remote' for AI agents" — Informatica

Introduced by Anthropic (late 2024), MCP is an open standard for connecting AI agents to external systems.

### Architecture

```
AI Agent
    │
    ▼
┌─────────────────────┐
│  MCP (Unified)       │
└─────────────────────┘
    │    │    │
    ▼    ▼    ▼
┌─────┐ ┌─────┐ ┌─────┐
│Browser│ │Slack│ │DB   │
│ Use  │ │ MCP │ │ MCP │
└─────┘ └─────┘ └─────┘
```

### MCP Integration in Browser-Use

Browser-Use already supports MCP for extensibility.

---

## Summary

| Topic | Key Takeaway |
|-------|--------------|
| **Browser-Use** | Browser-as-a-tool for AI agents |
| **CDP** | Browser's remote API (WebSocket) |
| **Agent Perception** | Screenshot + Interactive Elements |
| **Scraping** | Agent-driven, adaptive, flexible |
| **Desktop/Mobile** | Emerging (Claude, Mobile-Agent-E) |
| **Future** | MCP + agent-first dual ecosystem |

---

## Quick Start

```bash
uv add browser-use
uvx browser-use install
```

```python
from browser_use import Agent, Browser, ChatBrowserUse

async def main():
    browser = Browser()
    agent = Agent(
        task="Find product prices on this page",
        llm=ChatBrowserUse(),
        browser=browser,
    )
    await agent.run()

if __name__ == "__main__":
    import asyncio
    asyncio.run(main())
```

---

## Resources

- [Browser-Use GitHub](https://github.com/browser-use/browser-use)
- [MCP Documentation](https://modelcontextprotocol.io/)
- [Claude Computer Use](https://www.anthropic.com/claude-computer-use)

---

**Created:** February 2025
