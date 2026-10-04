# ERUX Astrology & Ephemeris MCP Server

[![MCP Registry](https://img.shields.io/badge/MCP%20Registry-io.github.akashagl92%2Ferux--astrology-blue?logo=github)](https://registry.modelcontextprotocol.io/v0.1/servers/io.github.akashagl92%2Ferux-astrology/versions/1.0.0)
[![Transport](https://img.shields.io/badge/Transport-Streamable%20HTTP%20SSE-success)](#quickstart)
[![Version](https://img.shields.io/badge/Version-1.0.0-brightgreen)](#)
[![Discord](https://img.shields.io/badge/Discord-Join%20Community-5865F2?logo=discord&logoColor=white)](https://discord.gg/cykwmxQ3K)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

The official **Model Context Protocol (MCP)** server for [ERUX](https://erux.ai) — the high-precision astronomical ephemeris and dual-zodiac astrology intelligence engine.

Connect **Claude Desktop**, **Cursor**, **Windsurf**, **Open-WebUI**, **LibreChat**, or autonomous agent frameworks directly to deterministic astrological calculations without compiling C ephemeris libraries or running local Python background processes.

---

## 🌟 Key Capabilities

- **High-Precision Ephemeris Calculations**: Sub-arcsecond planetary coordinates for Sun, Moon, Mars, Mercury, Jupiter, Venus, Saturn, Rahu, Ketu, Uranus, Neptune, Pluto, and Chiron.
- **Hybrid Insight Model (Dual-Zodiac Decoupling)**: Simultaneously evaluates Western Tropical Placidus (psychological/temperamental wiring) alongside Vedic Sidereal Lahiri (karmic timing and dasha epochs).
- **120-Year Parashara Vimshottari Timeline**: Multi-level Mahadasha, Antardasha, and Pratyantardasha hierarchy with exact date boundaries down to the hour.
- **Real-Time Transit Horoscopes**: Continuous tracking of planetary transits against natal chart cusps with structured transit aspect predictions.
- **Topocentric 24-Hora & Muhurta Engine**: Dynamic planetary hours (Horas) tied to local solar sunrise/sunset, plus Rahu Kaal, Yamaganda, Gulika, and Abhijit Muhurta.
- **Classical Vedic Yoga Detection**: Algorithmic identification of Raja, Dhana, Pancha Mahapurusha, and Gaja Kesari yogas with planetary trigger explanations.
- **Interactive Visual Wheel Generation**: Tools automatically return interactive visual chart wheel URLs (`https://erux.ai/chart?id=...`) and rendered PNG previews.

---

## 🚀 Omnichannel Bot Ecosystem & Community

ERUX provides **1:1 command parity** across its MCP server, web application, and chat integrations:

| Platform | Channel / Access | Features |
| :--- | :--- | :--- |
| **MCP Server** | `https://api.erux.ai/mcp` | Direct integration for Claude, Cursor, Windsurf, Open-WebUI, and AI Agents |
| **Discord Bot** | [Add to Discord Server](https://discord.com/oauth2/authorize?client_id=1532467179491164183) | Interactive slash commands: `/chart`, `/horoscope`, `/dasha`, `/yogas`, `/ask` |
| **Discord Community** | [Join Community](https://discord.gg/cykwmxQ3K) | Astrologer community discussions, release updates, and developer support |
| **Google Chat Bot** | Workspace Integration | Direct Google Chat app for organizational horoscopes and mundane cycles |
| **Web Platform** | [erux.ai](https://erux.ai) | Full visual interactive wheels, multi-varga divisional charts (D1–D60), transits |

---

## ⚡ Quickstart: Adding to Your MCP Client

ERUX runs as a **remote Streamable HTTP (SSE) server**. No local package installation, `pip install`, or `npx` compilation is required.

### 1. Claude Desktop
Add this to your `claude_desktop_config.json`:

* **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
* **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "erux-astrology": {
      "url": "https://api.erux.ai/mcp"
    }
  }
}
```

> **Using with `mcp-remote` (optional stdio bridge):**
> If your client strictly requires a `stdio` process:
> ```json
> {
>   "mcpServers": {
>     "erux-astrology": {
>       "command": "npx",
>       "args": ["-y", "mcp-remote", "https://api.erux.ai/mcp"]
>     }
>   }
> }
> ```

---

### 2. Cursor IDE
Add to `.cursor/mcp.json` (or via Cursor Settings > Features > MCP):

```json
{
  "mcpServers": {
    "erux-astrology": {
      "url": "https://api.erux.ai/mcp"
    }
  }
}
```

---

### 3. Windsurf (Codeium)
Add to your `mcp_config.json`:

```json
{
  "mcpServers": {
    "erux-astrology": {
      "serverUrl": "https://api.erux.ai/mcp"
    }
  }
}
```

---

### 4. Open-WebUI & LibreChat
Navigate to **Admin Panel > Settings > Tools / MCP** and register the remote SSE endpoint:
- **Server Name**: `erux-astrology`
- **Server URL**: `https://api.erux.ai/mcp`
- **Transport**: `sse` / `streamable-http`

---

### 5. Python / LangChain / CrewAI Agents

```python
from mcp import ClientSession
from mcp.client.sse import sse_client

async def run_astrology_agent():
    async with sse_client("https://api.erux.ai/mcp") as (read, write):
        async with ClientSession(read, write) as session:
            await session.initialize()
            
            # List available tools
            tools = await session.list_tools()
            print([tool.name for tool in tools.tools])
            
            # Calculate birth chart
            result = await session.call_tool("calculate_chart", {
                "date": "1992-10-14",
                "time": "14:30",
                "city": "Austin, Texas",
                "system": "hybrid"
            })
            print(result)
```

---

## 🛠️ Tool Catalog

ERUX exposes 6 deterministic tools to the host model:

### 1. `calculate_chart`
*Calculates personal birth charts or national mundane charts.*
- **Arguments**:
  - `date` (*string*, YYYY-MM-DD): Birth date or national founding date.
  - `time` (*string*, HH:MM): 24-hour birth time (defaults to `12:00` solar noon if unknown).
  - `city` (*string*): City of birth or founding.
  - `country` (*optional string*): Country to disambiguate city name.
  - `system` (*optional string*): `'hybrid'` (default), `'vedic'`, or `'western'`.
- **Outputs**: Exact planetary longitudes, retrogrades, nakshatras, house cusps, active Dasha cycle, and interactive `chart_ui_url`.

### 2. `get_horoscope`
*Generates transit horoscopes, daily 24-Hora planetary hours, and transit aspect predictions.*
- **Arguments**:
  - `birth_date` (*string*, YYYY-MM-DD): User's natal birth date.
  - `birth_city` (*string*): Natal birth city.
  - `birth_time` (*optional string*, default `'12:00'`): Natal birth time.
  - `current_city` (*optional string*): Relocated city for local topocentric horizon.
  - `cadence` (*optional string*): `'daily'` (Horas & Panchang), `'weekly'`, `'monthly'`, `'yearly'` (Solar Return).
  - `target_date` (*optional string*): Future target forecast date.
- **Outputs**: Real-world strategic guidance, topocentric 24-Hora schedule, Rahu Kaal, transit predictions, and interactive transits wheel link.

### 3. `calculate_dasha`
*Computes the 120-year Parashara Vimshottari planetary timeline.*
- **Arguments**:
  - `date` (*string*, YYYY-MM-DD): Natal birth date.
  - `time` (*string*, HH:MM): 24-hour birth time.
  - `city` (*string*): Birth city.
  - `target_date` (*optional string*): Target date to inspect active sub-periods.
- **Outputs**: 120-year Mahadasha timeline with full Antardasha and Pratyantardasha start/end dates.

### 4. `get_yogas`
*Identifies classical Vedic yogas and active planetary formations.*
- **Arguments**:
  - `date` (*string*, YYYY-MM-DD): Natal birth date.
  - `time` (*string*, HH:MM): 24-hour birth time.
  - `city` (*string*): Birth city.
- **Outputs**: Detected yogas (Raja, Dhana, Gajakesari, Mahapurusha), strength assessments, and planetary triggers.

### 5. `ask_astrology_guidance`
*Contextual life pillar synthesis grounded in house significations and Karakas.*
- **Arguments**:
  - `prompt` (*string*): The user's life question (e.g. career pivot, relocation timing).
  - `birth_date` (*string*): Natal birth date.
  - `birth_city` (*string*): Natal birth city.
- **Outputs**: Grounded astrological framework guidance with relevant house and Karaka analysis.

### 6. `get_interpretation`
*Comprehensive deep-dive analysis across canonical life pillars.*
- **Arguments**:
  - `pillar` (*string*): `'career'`, `'love'`, `'wealth'`, `'health'`, or `'spirituality'`.
  - `date` (*string*): Natal birth date.
  - `city` (*string*): Natal birth city.
- **Outputs**: Detailed analysis combining natal placements, dasha cycles, and transit influences.

---

## 💬 Example Prompts

Ask your MCP-connected model:

- *"What is my current Vimshottari Mahadasha and Antardasha? Born October 14, 1992 at 2:30 PM in Austin, Texas."*
- *"Show my complete birth chart wheel and calculate my Vedic yogas."*
- *"What are the best astronomical Horas for business negotiations today in London?"*
- *"Calculate the national mundane chart for France and summarize its key planetary placements."*
- *"I'm planning a career transition next month. Based on my natal placements, what planetary transits are active?"*

---

## 🔒 Security & Architecture

- **Hosted Remote Endpoint**: All astronomical calculations execute on secure, hardened ERUX cloud infrastructure (`api.erux.ai`).
- **No Local Code Execution**: Connecting to ERUX does not execute arbitrary binaries or scripts on your local machine.
- **Zero API Keys Required**: The public MCP endpoint requires no authentication or upfront API keys for standard queries.
- **Read-Only & Idempotent**: All tools are annotated as read-only and idempotent, ensuring safe execution within enterprise agent workflows.

---

## 📚 Resources & Links

- **Web Application**: [https://erux.ai](https://erux.ai)
- **Developer MCP Documentation**: [https://erux.ai/developers/mcp](https://erux.ai/developers/mcp)
- **Discord Bot**: [Invite Bot](https://discord.com/oauth2/authorize?client_id=1532467179491164183)
- **Community Discord**: [Join Community](https://discord.gg/cykwmxQ3K)
- **Official MCP Registry**: [Registry Link](https://registry.modelcontextprotocol.io/v0.1/servers/io.github.akashagl92%2Ferux-astrology/versions/1.0.0)

---

## 📄 License

This repository and its configuration schemas are licensed under the [MIT License](LICENSE).
Calculations and data provided via the ERUX API are subject to the [ERUX Terms of Service](https://erux.ai/terms).
