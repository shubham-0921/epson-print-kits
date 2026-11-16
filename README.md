# Epson business print kits

Claude plugins that bundle the Epson Print MCP connector with skills for a specific kind of business. Each
skill knows that business's everyday print jobs. It has a tested HTML layout for each one, plus the paper,
quality and colour settings to use. Claude fills in the layout and prints it through `print_document`.

| Plugin | Skill | Templates |
|---|---|---|
| `epson-restaurant` | `restaurant-printing` | menu, table tent, specials board, allergen cards (8/sheet), prep labels (24/sheet) |
| `epson-school` | `school-printing` | worksheet + answer key, certificate, attendance register, name tags (8/sheet), flashcards (8/sheet, double-sided) |

Each plugin's `.mcp.json` points at the remote connector
`https://epson-mcp-server.vercel.app/api/mcp`. The first tool call opens the Epson sign-in (OAuth).

## Install

Claude Code:

```bash
claude plugin marketplace add shubham-0921/epson-print-kits
```

```bash
claude plugin install epson-restaurant@epson-print
```

claude.ai / Claude Desktop: add the connector under Settings → Connectors (the URL above). Then upload a
skill folder, zipped, under Settings → Capabilities → Skills, e.g. `epson-restaurant/skills/restaurant-printing`.

## Template rules

Every template is designed for both A4 and Letter:
- Templates never set `@page { size }`. The server sizes the page to the chosen paper.
- Layouts use `vh` units, so they fill whichever paper is chosen.
- `<meta name="epson-paper">` lists the papers a template is tested on.
- `<meta name="epson-pages">` gives the expected page count.

Templates are checked with a render script in the main Epson Print MCP repo, which runs them through the same Chromium pipeline the server uses.
