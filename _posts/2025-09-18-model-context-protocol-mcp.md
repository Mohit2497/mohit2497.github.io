---
title: "Model Context Protocol (MCP) — Custom Tool for AI"
date: 2025-09-18 10:00:00 +0000
categories: [Blog, AI]
tags: [mcp, tools, tutorial, python]
excerpt: "Building an MCP server"
image:
  path: /assets/img/blog/mcp-server.png
  alt: MCP server overview
---

I've been experimenting with AI assistants and recently built a small backend using the Model Context Protocol (MCP). Following the excellent MCP tutorial linked below, I documented what I learned while building a working MCP server and connecting it to Claude Desktop.

## What MCP Let Me Do

In short, MCP allowed me to extend an LLM with real-world capabilities. Instead of treating the model as a closed box, MCP provides a protocol to expose tools and resources the model can call during conversations.

Key concepts I used:

- MCP server — the backend that hosts tools and resources.
- MCP client — the AI interface (e.g., Claude Desktop) that talks to the server.
- Tools — Python functions decorated as MCP tools (I implemented `get_latest_news`).
- Resources & prompts — files and templates that the model can reference when composing responses.

## Quick setup steps

The tutorial made setup straightforward. My minimal workflow was:

1. Add the dependencies to a `requirements.txt` file:

```text
fastapi
uvicorn
python-dotenv
httpx
beautifulsoup4
mcp
```

2. Install the packages:

```bash
pip install -r requirements.txt
```

3. Run the MCP server:

```bash
python main.py
```

After these steps, the server is ready for the client to connect.

## What `main.py` does (high level)

- Imports necessary libraries (`FastMCP`, `httpx`, `BeautifulSoup`, etc.).
- Creates an MCP server instance: `mcp = FastMCP("tech_news")`.
- Registers a tool with `@mcp.tool()` — my `get_latest_news` function that accepts a source name and returns headlines.
- Uses helper code (`fetch_news`) to request web pages and parse them with BeautifulSoup.
- Starts the server with `mcp.run(transport='stdio')` when executed directly.

In practice, calling the tool from the client runs the corresponding Python function on the server and returns structured results to the model. It’s a clean way to safely provide external data to the LLM.

## Connecting to Claude Desktop

To make the client use my server, I added a simple entry in `claude_desktop_config.json` under the Developer settings:

```json
{
  "mcpServers": {
    "tech_news_server": {
      "command": "python",
      "args": ["main.py"]
    }
  }
}
```

After restarting Claude Desktop, a small hammer icon appeared in the prompt area — indicating my MCP server was available as a tool. I could then ask Claude to fetch the latest headlines and the model would call my `get_latest_news` tool.

## Takeaways

This tiny project taught me a lot about safely extending LLMs with external logic. A few things stood out:

- MCP is an elegant, lightweight way to expose deterministic tools to a model.
- Tooling like `httpx` + `BeautifulSoup` lets you provide live web data to models without giving them raw web access.
- Keeping the server simple and well-scoped makes it easier to verify outputs and control behavior.

If you want to try it, the tutorial I followed is a great starting point:

[Model Context Protocol (MCP) — an end-to-end tutorial (hands-on)](https://busesenol.medium.com/model-context-protocol-mcp-an-end-to-end-tutorial-with-hands-on-project-c6b9ffee03d7)

Feel free to reach out if you want the `main.py` snippet or a sandboxed example to try locally.

## Repository

You can find the project source code in the repo:

- [MCP server project (source)](https://github.com/Mohit2497/Project-Based-Learnings/tree/main/mcp-server-project)