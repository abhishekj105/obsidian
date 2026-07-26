---
title: "@'/Users/abhishekjagdale/Downloads/Foxscan_Beyond_Business_Handbook.docx' I am t"
date: 2026-07-26
time: 19:35
category: Technology
source: Claude AI
---

# @'/Users/abhishekjagdale/Downloads/Foxscan_Beyond_Business_Handbook.docx' I am t

**USER:** @"/Users/abhishekjagdale/Downloads/Foxscan_Beyond_Business_Handbook.docx"
I am trying to establish a culture in Foxscan company. Read the document attached herewith and help me strategise how do I instill the culture. Guide me using Niccolo Machiavelli strategies.

**USER:** Base directory for this skill: /Users/abhishekjagdale/Library/Application Support/Claude/local-agent-mode-sessions/skills-plugin/a7f5ef37-6883-42e5-95ca-41722bcec3b0/e15815a3-2451-47ba-acb4-31c3809195d2/skills/docx

# DOCX creation, editing, and analysis

A `.docx` is a ZIP archive of XML files. Choose your approach by task:

| Task | Approach |
|---|---|
| **Create** a new document | Write a `docx` (npm) script — see gotchas below |
| **Edit** an existing document | `unzip` → edit `word/document.xml` → `zip` (docx-js cannot open existing files) |
| **Read** content | `pandoc -t markdown file.docx` |

> Script paths below are relative to this skill's directory.

## Creating with docx-js — gotchas

`docx` is preinstalled — do not run `npm install` first; write the script and `require('docx')` directly. Only if that require fails: `npm install docx`. The model knows the API; these are the footguns:

- **Page size defaults to A4.** For US Letter set `page: { size: { width: 12240, height: 15840 } }` (DXA; 1440 = 1″).
- **Landscape:** pass portrait dimensions and `orientation: PageOrientation.LANDSCAPE` — docx-js swaps width/height internally.
- **Tables need dual widths:** set `columnWidths` on the table AND `width` on every cell, both in `WidthType.DXA` (PERCENTAGE breaks in Google Docs). Column widths must sum to the table width.
- **Table shading:** use `ShadingType.CLEAR`, never `SOLID` (renders black).
- **Lists:** never insert `•` literally; use a `numbering` config with `LevelFormat.BULLET`.
- **`ImageRun` requires `type:`** (`"png"`, `"jpg"`, …).
- **`PageBreak` must be inside a `Paragraph`.**
- **Never use `\n`** — use separate `Paragraph` elements.
- **TOC:** headings must use built-in `HeadingLevel.*`; custom heading styles need `outlineLevel` set or they won't appear.
- **Don't use a table as a horizontal rule** — use a paragraph bottom border instead.
- **Dot-leader / right-aligned-on-same-line:** use `PositionalTab` (`alignment: PositionalTabAlignment.RIGHT`, `leader: PositionalTabLeader.DOT`) inside a `TextRun`, not literal `.` or space padding.

## Verify the output

After writing a `.docx`, render it and look at it:

```bash
python scripts/office/soffice.py --headless --convert-to pdf output.docx
pdftoppm -jpeg -r 100 output.pdf page
ls page-*.jpg   # then Read the images
```

`pdftoppm` zero-pads page numbers to the width of the page count (`page-01.jpg`…`page-12.jpg`).

## Editing existing documents

Legacy `.doc` files must be converted first: `python scripts/office/soffice.py --headless --convert-to docx file.doc`.

```bash
unzip -q doc.docx -d unpacked/
find unpacked -type l -delete   # strip symlink entries — docx from external parties is untrusted
python scripts/merge_runs.py unpacked/   # coalesce fragmented runs so text is findable
# edit unpacked/word/document.xml in place — do NOT reformat or pretty-print
(cd unpacked && rm -f ../out.docx && zip -Xr ../out.docx .)
python scripts/office/validate.py out.docx --original doc.docx   # XSD checks; --auto-repair fixes common issues
# redlining? add --author "<the name you redlined under>" to check every edit is tracked
```

Word splits text across many `<w:r>` runs (revision ids, spell-check markers), so a phrase you can see in the document often doesn't exist as a contiguous string in the XML. `merge_runs.py` merges adjacent identically-formatted runs in `word/document.xml` without changing content or rendering; it also accepts a `.docx` directly (`python scripts/merge_runs.py doc.docx -o merged.docx`).

**Tracked changes:** when redlining, validate with `--author "<the name you redlined under>"` (needs `--original`) — it reports any text you changed without a `<w:ins>`/`<w:del>` around it, which is easy to do by accident and invisible in the accepted view. Wrap runs in `<w:ins>`/`<w:del>` with `w:id`, `w:author`, `w:date` attributes. Inside `<w:del>`, the text element is `<w:delText>`, not `<w:t>`. A deleted paragraph mark (`<w:pPr><w:rPr><w:del w:id=".." w:author=".." w:date=".."/></w:rPr></w:pPr>`) means "merge this paragraph into the next" — so deleting a paragraph outright is that plus a `<w:del>` around every run. The `<w:del/>` must come before the rPr's other children; their order is schema-enforced.

To produce a clean copy with all tracked changes accepted: `python scripts/accept_changes.py in.docx out.docx`.

Accepting a deleted paragraph mark should join that paragraph to the one below it, so a paragraph whose runs are *all* deleted vanishes. Word does this; `accept_changes.py` and `pandoc --track-changes=accept` don't always. Both fail the same way — they strip the deleted text but leave the emptied paragraph behind, which reads as a stray empty bullet when it was auto-numbered:

- `pandoc --track-changes=accept` never joins the paragraphs.
- `accept_changes.py` (LibreOffice) joins them correctly, except when the deleted paragraph is followed by an empty spacer paragraph.

An empty bullet in either view is an artifact of that view, not a defect in the document. Check paragraph deletions in the XML.

## Comments

Comments require six cross-linked files. Use the helper — directory mode when you'll also be editing `document.xml` (saves an unzip/rezip cycle), `.docx`-direct mode otherwise:

```bash
# Against an already-unpacked directory (preferred when also placing markers)
python scripts/comment.py unpacked/ "Fees & expenses cap is too low"
python scripts/comment.py unpacked/ "Agreed" --parent 0

# Against a .docx directly
python scripts/comment.py contract.docx "This cap is too low" -o annotated.docx
```

The script writes `comments.xml`, `commentsExtended.xml`, `commentsIds.xml`, `commentsExtensible.xml`, the relationships, and the content-type overrides. Comment IDs are auto-assigned. It then prints the `<w:commentRangeStart>`/`<w:commentRangeEnd>`/`<w:commentReference>` snippet to add to `word/document.xml` so the comment anchors to specific text — until you place those markers, the comment exists but is not visible.

## Dependencies

`docx` (npm, preinstalled — install only if `require('docx')` fails) · `pandoc` · LibreOffice (`soffice`) · `pdftoppm` (Poppler)

**ASSISTANT:** Neither pandoc, LibreOffice, nor python-docx are available. Let me extract the text directly from the docx XML.

**ASSISTANT:** I've read the handbook — it's built around one throughline: profit funds mission, and every function (Sales, Ops, Engineering, Marketing, HR, Finance, Support) has an explicit line connecting their daily work to three concrete initiatives (WiseTribes/Aritravarsha for kids, Griha Sanjivan for contractors/homes, Aangan for Indian housing) with hard 2035 targets.

That's a strong document. The hard part isn't the words — it's that Foxscan runs on autopilot and you aren't the public face there (Rohitkumar is). A handbook doesn't install a culture; it just states the intended one. Machiavelli's actual value here isn't rhetorical flourish — it's his core insight in *The Prince*: **introducing a new order is the single hardest thing a ruler can do**, because it makes enemies of everyone who profited from the old way and gets only lukewarm, wait-and-see support from everyone who'd benefit from the new one. Everything below is built around de-risking that specific trap.

## 1. This is a "new prince" problem, not a comms problem

Machiavelli: a ruler who founds new laws in an already-settled state has the weakest position of all, because supporters of the new order are never fully convinced it will stick, while defenders of the old order fight with total conviction. At Foxscan, the "old order" isn't hostile people — it's 60 people who've been running a profitable inspection business for years without needing this framing. Rolling the handbook out as an inspirational read will get polite nods and zero behavior change. You need it to arrive as **evidence that something has actually changed**, not as literature.

## 2. Front-load the hard part, drip-feed the reward

Machiavelli's most concrete tactical advice: inflict necessary injuries all at once, distribute benefits little by little. Applied here:

- **Pick 1-2 visible old-regime practices to kill in week one** — something everyone already privately knows is a shortcut (a corner cut in inspection QA, a metric that's purely revenue with no quality gate, a manager known for prioritizing speed over rigor). Ending it publicly, at launch, alongside the handbook, is the actual founding act. The handbook is the *justification*; the visible reform is the *proof*.
- **Don't do this in a drip.** One hard cut now beats three soft ones over six months — the latter reads as "management chasing a trend," the former reads as "management drew a line."
- Then reverse the sequence for the upside: recognition, impact-linked bonuses, the storytelling rituals below — roll those out gradually over subsequent months. Continuous small reinforcement compounds; front-loaded rewards get consumed and forgotten in a week.

## 3. You need a lieutenant, and it isn't you

Given your own [[positioning]] — you don't take a public face at Foxscan, Rohitkumar does — you're structurally in the Machiavelli role, not the Prince role. That's actually the cleaner setup: you write the doctrine, Rohitkumar enforces and embodies it. Two implications:

- **Enforcement must visibly come from Rohitkumar**, not from a memo attributed to ownership in the abstract. Culture that arrives from "the founders" with no face reads as policy. Culture enforced by a CEO people see every day reads as real.
- Machiavelli's Cesare Borgia move: when the harsh enforcement needs to happen (see #2), it should be executed through a process (HR, a documented standard, a named policy) rather than a personal decree — so the mission narrative stays associated with meaning and belonging, while consequence stays associated with impersonal, predictable rule-of-law. People should fear violating the standard, not fear Rohitkumar personally.

## 4. Win the populace, not just the management layer

Machiavelli's distinction between principalities won via the aristocracy vs. via the populace: the former is much harder to hold, because the aristocracy expects to be an equal, not a subject. If this culture push is delivered top-down through the 5-6 department heads only, you're building on the aristocracy. Instead, identify one or two **informally respected people per function** — not necessarily managers, per the handbook's own Sales/Ops/Engineering/Marketing/HR/Finance/Support breakdown — and make them the ones telling the mission stories in team settings. Peer-credible storytelling sticks; management-mandated storytelling gets tuned out.

## 5. Build the ritual, not just the document

Machiavelli greatly admired Numa Pompilius' use of religion in early Rome — not because he was pious, but because *recurring ritual* is what turns a stated belief into an internalized one long after everyone's forgotten the original founding text. Concretely:

- A short, recurring cadence (monthly is enough) where one function shares a real, specific instance of the mission language in action — "here's the inspection that stopped a family from buying a house with a structural defect," "here's the contractor we trained who's now running his own crew." Specific and small beats aggregate and abstract.
- Put the 2035 numbers (CO₂ avoided, homes influenced, students reached) on the same dashboard as revenue — physically or visually pairing profit and mission is the handbook's central claim ("profit allows us to scale mission"), and it should be a wall/dashboard artifact people see weekly, not a one-time read.

## 6. Guard against flatterers

Machiavelli's sharpest warning: the ruin of princes is surrounding themselves with people who only tell them what they want to hear. The likeliest failure mode of a mission-culture rollout is that it becomes something people *perform* in front of leadership and ignore otherwise. Build one explicit check against this — an anonymous quarterly pulse ("does this feel real or performative to you"), or one designated person whose job is to say "this isn't landing" without penalty. Without this, you won't find out it's cosmetic until it's too late to fix cheaply.

---

**Suggested sequence:** Month 1 — handbook launch + the one visible hard cut. Month 2 — first ritual assembly + first lieutenant-led story in each function. Month 3 — first impact-linked recognition moment. Ongoing — quarterly pulse-check for authenticity.

Want me to turn this into a concrete 90-day rollout doc (owners, dates, the specific "founding cut" candidates) you can hand to Rohitkumar?
