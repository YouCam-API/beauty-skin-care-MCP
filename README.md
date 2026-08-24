![YouCam MCP for Beauty &amp; Skin Care](assets/banner-beauty.png)

[![CONSOLE](https://img.shields.io/badge/YOUCAM-3183FF?style=for-the-badge)](https://yce.perfectcorp.com/api-console/)
[![DOCUMENT](https://img.shields.io/badge/DOCUMENT-FF2D78?style=for-the-badge)](https://docs.perfectcorp.com/develop/mcp.md)

Official Perfect Corp [Model Context Protocol (MCP)](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro) server for beauty and skin care AI. This server lets MCP clients like [Claude Desktop](https://claude.ai/download), [Cursor](https://www.cursor.com), [Github Copilot](https://github.com/features/copilot), [Codex](https://openai.com/codex) and others analyse skin and hair, try on makeup, hairstyles and nails, refine facial features, and simulate body and aging changes — all from a single photo.

The client handles request formatting, authentication and asynchronous polling. You add one entry to a config file and start prompting.

**Server URL:** `https://mcp-api-01.makeupar.com/mcp/beauty`

---

## Quickstart with Claude Desktop

1. Get your API key from the [YouCam API Console](https://yce.perfectcorp.com/api-console/en/api-keys/).
2. Install [Node.js and npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).
3. Go to **Claude → Settings → Developer → Edit Config → `claude_desktop_config.json`** and include the following:

```json
{
  "mcpServers": {
    "youcam-beauty": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp-api-01.makeupar.com/mcp/beauty",
        "--header",
        "Authorization:${AUTH_HEADER}"
      ],
      "env": {
        "AUTH_HEADER": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

4. Quit Claude Desktop completely via **File → Exit** — closing the window is not enough — then reopen it.

---

## Other MCP clients

### Cursor

1. Open **Settings → Tools & MCP → Add Custom MCP**.
2. Add the following to `mcp.json`:

```json
{
  "mcpServers": {
    "youcam-beauty": {
      "url": "https://mcp-api-01.makeupar.com/mcp/beauty",
      "type": "http",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

3. Back in **Settings → Tools & MCP**, enable the tools you want under *Installed MCP Servers*.

### Copilot in VS Code

Run `> MCP: Add Server`, choose **HTTP**, enter the server URL, name the server, and set the `Authorization` header.

Or run `> MCP: Open User Configuration` and add the following to `mcp.json`:

```json
{
  "servers": {
    "youcam-beauty": {
      "url": "https://mcp-api-01.makeupar.com/mcp/beauty",
      "type": "http",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

### Any other client

Any client that supports remote MCP over HTTP works with the same three values: the URL, an `Authorization` header, and the literal `Bearer ` prefix in front of your key. Configuration is static — changing the key requires a client reload.

---

## What you can build

| Use case | What the server does |
| --- | --- |
| Skincare diagnostics | Scores wrinkles, pores, acne, oiliness, dark circles, texture and hydration; classifies Fitzpatrick skin type; simulates before/after treatment progress |
| Shade matching | Detects skin, eye, brow, lip and hair colour, then applies makeup with per-feature control |
| Salon and haircare consultation | Detects hair type, density, frizz and length; tries on styles, colour, bangs, waves, volume and extensions |
| Beauty e-commerce try-on | Makeup, nails, contact lenses, curated looks and men's beard styles |
| Aesthetic and wellness previews | Face lift and reshape, teeth whitening, smile generation, body reshaping, aging simulation |

The full tool list with parameters is in the [MCP documentation](https://docs.perfectcorp.com/develop/mcp.md).

---

## Example usage

**YouCam API units are consumed by these tools.**

Try asking Claude:

- "Analyse my skin from this selfie, score wrinkles, pores and hydration, and tell me which two concerns to prioritise"
- "Show me what I'd look like with a shoulder-length wavy bob in ash brown — list the templates you have before you pick one"
- "Run a Fitzpatrick skin type analysis on this photo, then recommend a foundation shade family and apply a natural everyday look"
- "Detect this customer's hair type and density, then simulate a volume try-on so I can show them the before and after"
- "Take my selfie and age it forward decade by decade, then tell me which skin concerns change the most"

---

## How tasks and file uploads work

Most beauty tools are **asynchronous**. The flow your client runs on your behalf:

1. **Provide an image.** `File_Upload` opens a drag-and-drop widget. `Get_Upload_API_Info` returns the endpoint if you'd rather upload programmatically and pass a file ID.
2. **Run the detection step where one exists.** Face lift, face reshape, body reshape and teeth whitening each have a matching `*_Detection` tool that must run first — it produces the parameters the main tool consumes.
3. **Start the task.** The tool returns a task identifier, not a finished image.
4. **Poll for the result.** `Get_Running_Task_Status` returns the current state and, once complete, the result URLs.
5. **Download the output.** Result URLs are hosted temporarily — fetch and store anything you need to keep.

> Result URLs returned by the server must be passed through unmodified. Do not rewrite, shorten or proxy them.

Describe the outcome you want rather than driving each call by hand — most clients will chain these steps automatically.

---

## Pricing and usage

Each tool consumes **units** from your YouCam API balance. Cost varies by feature, resolution mode and output count.

Ask before you build a flow — the `Get_Feature_Cost` tool answers directly:

> "What does AI_Makeup_Virtual_Try_On cost per call, and how does it compare to AI_Look_Virtual_Try_On?"

Balance, top-ups and usage history live in the [YouCam API Console](https://yce.perfectcorp.com/api-console/).

---

## Troubleshooting

**Server does not appear in the client.** Validate your JSON, confirm the config path against your client's documentation, and fully restart the client.

**Claude Desktop still shows nothing after editing the config.** The window was closed but the process kept running. Quit via **File → Exit**.

**`401 Unauthorized`.** Check the header reads `Bearer YOUR_API_KEY` — the `Bearer ` prefix is required. If the key was rotated, update it and reload the client.

**Tools are listed but every call fails.** Your network cannot reach `mcp-api-01.makeupar.com`. Check firewall, VPN and proxy rules for that host.

**"Face not detected" errors.** Use a front-facing, well-lit photo where the face is unobstructed and fills a reasonable portion of the frame.

**A task never finishes.** Call `Get_Running_Task_Status` again with the task ID before assuming failure. If it errors, the source image likely failed validation — re-upload a cleaner one.

**Insufficient units.** Check usage and top up in the [YouCam API Console](https://yce.perfectcorp.com/api-console/).

---

## Support

Questions, bug reports and feature requests: [YouCamOnlineEditor_API@perfectcorp.com](mailto:YouCamOnlineEditor_API@perfectcorp.com)

When reporting a problem, include your MCP client and version, the tool that failed, and the task ID if one was returned.

---

© Perfect Corp. YouCam is a trademark of Perfect Corp.
