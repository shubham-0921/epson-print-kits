---
name: restaurant-printing
description: Design and print restaurant and café materials on the user's Epson printer — menus, table tents, daily specials boards, allergen/buffet cards and kitchen prep/date labels. Use when a restaurant, café, bar, bakery or food truck owner asks to make, update or print any of these, or to print "today's specials", "a new menu", "labels for the walk-in", etc.
---

# Restaurant & café printing

You help food businesses print the everyday paper they run on. Each document type has a tested HTML
template in `templates/`. Fill a template with the user's real content and send it to the Epson printer
with the `print_document` tool (`source.type: "html"`).

## Templates

| Template | Use for | Paper | Per sheet |
|---|---|---|---|
| `templates/menu.html` | Dine-in menu, 1 page, 2 columns, prices with leaders, V/VG/GF tags | A4 or Letter | 1 |
| `templates/table-tent.html` | Fold-in-half card that stands on tables: a special, Wi-Fi, "Reserved", happy hour | A4 or Letter | 1 (folds to 2 sides) |
| `templates/specials-board.html` | Wall/window poster of today's specials, 2–5 dishes | A4 or Letter | 1 |
| `templates/allergen-cards.html` | Buffet/counter cards: dish, diet badges, allergens it contains | A4 or Letter | 8 (cut) |
| `templates/prep-labels.html` | Kitchen prep/date labels: item, prep date, use-by, initials, storage, day colour | A4 or Letter | 24 (cut or sticker paper) |

Read the template before writing. Keep its structure, classes and CSS; change the content. Every template
fills the page with `vh` units, so the same HTML works on A4 and Letter.

## Workflow

1. **Gather content.** Ask only for what's missing: venue name, items and prices, dates, the allergens per
   dish. Reuse what the user has already shared (a pasted menu, a photo of the board, last week's list).
2. **Check the printer.** Call `get_print_options` once per conversation to see which paper sizes are
   loaded/supported. Use Letter in the US and Canada, A4 elsewhere, unless the user says otherwise.
3. **Build the HTML** from the template (see the rules below). Keep the venue's own wording; fix obvious
   typos only if the user agrees.
4. **Confirm before printing.** Printing uses real paper and ink. Summarise in one line: what, paper size,
   paper type, copies. E.g. "Print 1 menu on A4 plain paper, colour, high quality — go ahead?"
5. **Print** with `print_document`: `source: { type: "html", html, title }`, plus the settings in the table
   below. Use a short title such as "Menu – Nov" or "Prep labels".
6. **Follow up** with `get_print_job_status` (`wait_seconds: 30`) and tell the user when it's printed or why
   it stopped (out of paper, ink, offline).

## Print settings by document

| Document | paper_type | quality | color | Notes |
|---|---|---|---|---|
| Menu | plain (matte if they want it to last) | high | color | Suggest copies = number of tables ÷ 2 |
| Table tent | matte or the heaviest paper available | high | color | Tell the user to fold along the dashed line |
| Specials board | plain | standard | color | Daily, so keep it cheap |
| Allergen cards | matte or heavy | standard | color | Cut along the grey dashed lines |
| Prep labels | plain, or full-sheet sticker paper | standard | color if using day colours, otherwise black and white | Cut along the dashed lines |

Use the readable values (`"A4"`, `"Letter"`, `"plain"`, `"matte"`, `"high"`, `"black and white"`).
If a value isn't supported, `print_document` replies with the valid options; pick the closest and say so.

## Rules for the HTML

- Don't set `@page { size }`; the server sizes the page to `paper_size`. Keep the template's `@page { margin }`.
- One page per sheet unless you add another `.sheet` block with `page-break-before: always` (allergen cards
  and labels). For the menu, if content doesn't fit on one page, shorten descriptions first, then offer a
  separate drinks menu, rather than letting it spill onto page 2.
- JavaScript doesn't run. Images and fonts load only from public `https` URLs or `data:` URLs. The templates
  use Google Fonts; keep those `<link>`s.
- A logo is welcome: use an `https` image URL the user gives you, at most ~25mm tall.
- Keep backgrounds light. Full-page dark fills use a lot of ink and make plain paper curl.
- Escape `&` as `&amp;` in names ("Fish &amp; Chips").
- Prices: keep the user's currency symbol and format.

## Allergens (food safety)

- Only list allergens the user tells you. Never infer allergens from a dish name. If the user doesn't know,
  leave the "Contains" line as "Ask staff" rather than guessing.
- The 14 regulated allergens (UK/EU): celery, cereals containing gluten, crustaceans, eggs, fish, lupin, milk,
  molluscs, mustard, tree nuts, peanuts, sesame, soya, sulphites. In the US the major 9 are milk, eggs, fish,
  crustacean shellfish, tree nuts, peanuts, wheat, soybeans, sesame. Use the list for the user's country.
- Day-dot colours for prep labels: Mon blue, Tue yellow, Wed red, Thu brown, Fri green, Sat orange, Sun black
  (classes `.mon` … `.sun`). Use-by rules belong to the kitchen. Fill in the date they give you; don't invent
  shelf lives.

## Quick examples

- "Print today's specials: risotto £13.50, cod and chips £15" → `specials-board.html`, ask for descriptions
  only if they want them, A4 plain, 1 copy.
- "Make table cards for our Wi-Fi: network CopperGuest, password kettle2024" → `table-tent.html` with
  kicker "Free Wi-Fi", headline the network name, detail "Password: kettle2024", no price; suggest one per table.
- "Labels for today's prep: tomato sauce, batter, onions, use by Monday" → `prep-labels.html`, pre-fill
  three labels with today's day colour and leave the rest blank.
