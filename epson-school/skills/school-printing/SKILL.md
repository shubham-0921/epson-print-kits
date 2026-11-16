---
name: school-printing
description: Create and print classroom materials on the user's Epson printer — worksheets with answer keys, award certificates, attendance registers, name tags/desk labels and double-sided flashcards. Use when a teacher, tutor, school office or parent homeschooling asks to make, print or reprint any of these, e.g. "a fractions worksheet for year 4", "certificates for my class", "name tags for Monday", "flashcards for this vocab list".
---

# School & tutoring printing

You help teachers and tutors turn lesson ideas into printed handouts. Each document type has a tested HTML
template in `templates/`. Write the content, fill the template, and send it to the Epson printer with the
`print_document` tool (`source.type: "html"`).

## Templates

| Template | Use for | Paper | Pages |
|---|---|---|---|
| `templates/worksheet.html` | Worksheet with name/class/date/score header, sections, 4 question types, answer key | A4 or Letter | 1 + answer key |
| `templates/certificate.html` | Award certificate: award, name, reason, signature and date | A4 or Letter | 1 per student |
| `templates/attendance-register.html` | Monthly register: 30 students × 20 school days + P/A/L totals | A4 or Letter | 1 |
| `templates/name-tags.html` | Name tags / desk labels with group colour, 8 per sheet | A4 or Letter | 1 per 8 students |
| `templates/flashcards.html` | Double-sided flashcards, 8 per sheet, backs mirrored for flipping | A4 or Letter | 2 per 8 cards |

Read the template before writing. Keep its structure, classes and CSS; replace the sample content (the
"Greenfield Primary" sample school, student names, questions). Every template fills the page with `vh`
units, so the same HTML works on A4 and Letter.

## Workflow

1. **Gather what you need.** Ask only for what's missing: subject, topic, age/grade, number of questions,
   student names, school name, teacher name, date. If the teacher pastes a class list, reuse it across
   certificates, name tags and the register.
2. **Write the content yourself** when asked (questions, answers, flashcard definitions). Pitch it to the
   stated age/grade and curriculum (e.g. "Year 4" UK, "4th grade" US). Check every answer in the key; a
   wrong key is worse than none.
3. **Check the printer.** Call `get_print_options` once per conversation. Use Letter in the US and Canada,
   A4 elsewhere, unless told otherwise.
4. **Confirm before printing.** Printing uses real paper and ink. One line: what, pages × copies, paper,
   colour. E.g. "Print 28 copies of the worksheet (page 1 only) + 1 answer key, A4 plain, black and white?"
5. **Print** with `print_document`: `source: { type: "html", html, title }` and the settings below. For
   class sets, print one student copy with `copies` = class size, and send the answer key as a separate
   job with 1 copy (remove the answer page from the student version).
6. **Follow up** with `get_print_job_status` (`wait_seconds: 30`) and report when it's done or why it stopped.

Jobs over 30 printed pages (pages × copies) need `confirm_large: true`, which you may set only after the
teacher agrees to that number of pages.

## Print settings by document

| Document | paper_type | quality | color | Notes |
|---|---|---|---|---|
| Worksheet | plain | standard (draft for rough practice sheets) | black and white unless colour matters | `copies` = class size |
| Answer key | plain | standard | black and white | Separate job, 1 copy |
| Certificate | matte or the heaviest paper available | high | color | 1 page per student in one job |
| Attendance register | plain | standard | black and white | 1 per class per month |
| Name tags | plain or matte | standard | color (group colours) | Cut along dashed lines |
| Flashcards | matte or heavy | standard | color | See "Double-sided" below |

Use readable values (`"A4"`, `"Letter"`, `"plain"`, `"matte"`, `"high"`, `"draft"`, `"black and white"`).
If a value isn't supported, `print_document` replies with the valid options; pick the closest and say so.

## Template notes

- **Worksheet.** Question types: `.grid` of `.sum` boxes (quick-fire), `.q` + `.mc` (multiple choice, 4
  options), `.q` + `.lines` with `style="--n:3"` (written answer, n lines), `.q` + `.box` (draw / show
  working). Keep the questions on one page: about 8 quick-fire + 4 multiple choice + 2 written. For longer
  sets, add another `.page`. Put marks per section in `.marks` and the total in `.score`.
- **Certificates for a class.** Repeat the `.cert` block once per student in a single HTML document; each
  prints on its own page. Personalise the reason line for each student if the teacher gives reasons;
  otherwise use one shared reason.
- **Attendance register.** Header dates are school days only (Mon–Fri) for 4 weeks. For a 5-week month,
  make a second register for the remaining days. Mark holidays with "—" in the date cell. Keep blank rows
  up to 30.
- **Name tags.** One `.tag` per student, 8 per `.sheet`; add a `.sheet` for each further 8. Group colours
  `.c1`–`.c6` (blue, green, orange, purple, red, teal). The `.icon` holds the initial or a single emoji.
- **Flashcards.** Fronts sheet then backs sheet, 8 cards each. The backs are mirrored per row
  (2,1 / 4,3 / 6,5 / 8,7) so answers line up when the sheet is flipped over its long edge.

## Double-sided printing

Most home and small-office Epson printers (e.g. EcoTank ET-2400) print one side at a time, and
`print_document` always prints every page of the HTML. Offer the teacher one of two methods:

- **Glue (simplest, sturdiest):** print the full template (fronts + backs) as one job, glue the two sheets
  back to back, then cut. Always lines up. Recommended for younger classes.
- **Flip:** send the fronts sheet alone as job 1 and wait until it's printed. Ask the teacher to put that sheet
  back in the tray so the blank side gets printed, flipped over its long edge (check the printer's paper-tray
  icon for which way up). Then send the backs sheet alone as job 2. Make each job a full HTML document
  containing only that `.sheet`.

## Rules for the HTML

- Don't set `@page { size }`; the server sizes the page to `paper_size`. Keep the template's `@page { margin }`.
- JavaScript doesn't run. Images and fonts load only from public `https` URLs or `data:` URLs. The templates
  use Google Fonts (Lexend is designed for reading ease; keep it for children's material).
- A school logo is welcome: an `https` image URL from the teacher, at most ~20mm tall.
- Escape `&` as `&amp;`. Fractions: use ½ ¼ ¾ ⅓ ⅔ characters or "3/4"; both render.
- Student names are personal data. Use only names the teacher gives you, and don't add other details
  (grades, needs, photos) unless asked.

## Quick examples

- "10-question times tables worksheet for 3rd grade, 25 copies" → `worksheet.html` with 8 quick-fire +
  2 word problems, Letter plain black and white, 25 copies of page 1, then the key as a separate job.
- "Star of the week certificates for Aisha and Ben" → `certificate.html` with two `.cert` blocks, "Star of
  the Week", A4 matte, high quality, colour.
- "Flashcards for these 8 French words" → `flashcards.html`, word on the front, English + example sentence
  on the back, explain the glue-or-flip step.
