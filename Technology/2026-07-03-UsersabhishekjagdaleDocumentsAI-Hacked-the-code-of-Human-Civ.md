---
title: "@'/Users/abhishekjagdale/Documents/AI Hacked the code of Human Civilization .doc"
date: 2026-07-03
time: 22:39
category: Technology
source: Claude AI
---

# @'/Users/abhishekjagdale/Documents/AI Hacked the code of Human Civilization .doc

**USER:** @"/Users/abhishekjagdale/Documents/AI Hacked the code of Human Civilization .docx"
Help me draft a book from the insights of Yuval Noah Harari on AI hacked the code of human civilization. Also suggest fascinating title and subtitle for the book. Written by Abhishek Varsha Jagdale

**ASSISTANT:** I'll start by reading your source document to understand Harari's insights, then draft the book.

**ASSISTANT:** I've read the full lecture — it's Harari's Oxford Tanner Lecture. This is rich material. Let me now build a proper book from it. I'll produce it as a Word manuscript you can open and edit. First, let me set up the document tooling.

**USER:** Base directory for this skill: /Users/abhishekjagdale/Library/Application Support/Claude/local-agent-mode-sessions/skills-plugin/a7f5ef37-6883-42e5-95ca-41722bcec3b0/e15815a3-2451-47ba-acb4-31c3809195d2/skills/docx

# DOCX creation, editing, and analysis

## Overview

A .docx file is a ZIP archive containing XML files.

## Quick Reference

| Task | Approach |
|------|----------|
| Read/analyze content | `extract-text`, or unpack for raw XML |
| Create new document | Use `docx-js` - see Creating New Documents below |
| Edit existing document | Unpack → edit XML → repack - see Editing Existing Documents below |

> **Script paths:** every `scripts/...` command in this document is relative to **this skill's directory** (the directory containing this `SKILL.md`). Run them from here, or prefix with the skill's absolute path.

### Converting .doc to .docx

Legacy `.doc` files must be converted before editing:

```bash
python scripts/office/soffice.py --headless --convert-to docx document.doc
```

### Reading Content

```bash
# Text extraction as markdown
extract-text document.docx

# Show tracked changes instead of accepting them
pandoc --track-changes=all document.docx -o output.md

# Raw XML access
python scripts/office/unpack.py document.docx unpacked/
```

### Converting to Images

```bash
python scripts/office/soffice.py --headless --convert-to pdf document.docx
pdftoppm -jpeg -r 150 document.pdf page
ls page-*.jpg
```

`pdftoppm` zero-pads the page number to the width of the total page count (so a 12-page PDF produces `page-01.jpg`…`page-12.jpg`, not `page-1.jpg`). Use the `ls` to get the actual filenames before reading them.

### Accepting Tracked Changes

To produce a clean document with all tracked changes accepted (requires LibreOffice):

```bash
python scripts/accept_changes.py input.docx output.docx
```

---

## Creating New Documents

Generate .docx files with JavaScript, then validate. Install: `npm install -g docx`

### Setup
```javascript
const { Document, Packer, Paragraph, TextRun, Table, TableRow, TableCell, ImageRun,
        Header, Footer, AlignmentType, PageOrientation, LevelFormat, ExternalHyperlink,
        InternalHyperlink, Bookmark, FootnoteReferenceRun, PositionalTab,
        PositionalTabAlignment, PositionalTabRelativeTo, PositionalTabLeader,
        TabStopType, TabStopPosition, Column, SectionType,
        TableOfContents, HeadingLevel, BorderStyle, WidthType, ShadingType,
        VerticalAlign, PageNumber, PageBreak } = require('docx');

const doc = new Document({ sections: [{ children: [/* content */] }] });
Packer.toBuffer(doc).then(buffer => fs.writeFileSync("doc.docx", buffer));
```

### Validation
After creating the file, validate it. If validation fails, unpack, fix the XML, and repack.
```bash
python scripts/office/validate.py doc.docx
```

### Page Size

```javascript
// CRITICAL: docx-js defaults to A4, not US Letter
// Always set page size explicitly for consistent results
sections: [{
  properties: {
    page: {
      size: {
        width: 12240,   // 8.5 inches in DXA
        height: 15840   // 11 inches in DXA
      },
      margin: { top: 1440, right: 1440, bottom: 1440, left: 1440 } // 1 inch margins
    }
  },
  children: [/* content */]
}]
```

**Common page sizes (DXA units, 1440 DXA = 1 inch):**

| Paper | Width | Height | Content Width (1" margins) |
|-------|-------|--------|---------------------------|
| US Letter | 12,240 | 15,840 | 9,360 |
| A4 (default) | 11,906 | 16,838 | 9,026 |

**Landscape orientation:** docx-js swaps width/height internally, so pass portrait dimensions and let it handle the swap:
```javascript
size: {
  width: 12240,   // Pass SHORT edge as width
  height: 15840,  // Pass LONG edge as height
  orientation: PageOrientation.LANDSCAPE  // docx-js swaps them in the XML
},
// Content width = 15840 - left margin - right margin (uses the long edge)
```

### Styles (Override Built-in Headings)

Use Arial as the default font (universally supported). Keep titles black for readability.

```javascript
const doc = new Document({
  styles: {
    default: { document: { run: { font: "Arial", size: 24 } } }, // 12pt default
    paragraphStyles: [
      // IMPORTANT: Use exact IDs to override built-in styles
      { id: "Heading1", name: "Heading 1", basedOn: "Normal", next: "Normal", quickFormat: true,
        run: { size: 32, bold: true, font: "Arial" },
        paragraph: { spacing: { before: 240, after: 240 }, outlineLevel: 0 } }, // outlineLevel required for TOC
      { id: "Heading2", name: "Heading 2", basedOn: "Normal", next: "Normal", quickFormat: true,
        run: { size: 28, bold: true, font: "Arial" },
        paragraph: { spacing: { before: 180, after: 180 }, outlineLevel: 1 } },
    ]
  },
  sections: [{
    children: [
      new Paragraph({ heading: HeadingLevel.HEADING_1, children: [new TextRun("Title")] }),
    ]
  }]
});
```

### Lists (NEVER use unicode bullets)

```javascript
// ❌ WRONG - never manually insert bullet characters
new Paragraph({ children: [new TextRun("• Item")] })  // BAD
new Paragraph({ children: [new TextRun("\u2022 Item")] })  // BAD

// ✅ CORRECT - use numbering config with LevelFormat.BULLET
const doc = new Document({
  numbering: {
    config: [
      { reference: "bullets",
        levels: [{ level: 0, format: LevelFormat.BULLET, text: "•", alignment: AlignmentType.LEFT,
          style: { paragraph: { indent: { left: 720, hanging: 360 } } } }] },
      { reference: "numbers",
        levels: [{ level: 0, format: LevelFormat.DECIMAL, text: "%1.", alignment: AlignmentType.LEFT,
          style: { paragraph: { indent: { left: 720, hanging: 360 } } } }] },
    ]
  },
  sections: [{
    children: [
      new Paragraph({ numbering: { reference: "bullets", level: 0 },
        children: [new TextRun("Bullet item")] }),
      new Paragraph({ numbering: { reference: "numbers", level: 0 },
        children: [new TextRun("Numbered item")] }),
    ]
  }]
});

// ⚠️ Each reference creates INDEPENDENT numbering
// Same reference = continues (1,2,3 then 4,5,6)
// Different reference = restarts (1,2,3 then 1,2,3)
```

### Tables

**CRITICAL: Tables need dual widths** - set both `columnWidths` on the table AND `width` on each cell. Without both, tables render incorrectly on some platforms.

```javascript
// CRITICAL: Always set table width for consistent rendering
// CRITICAL: Use ShadingType.CLEAR (not SOLID) to prevent black backgrounds
const border = { style: BorderStyle.SINGLE, size: 1, color: "CCCCCC" };
const borders = { top: border, bottom: border, left: border, right: border };

new Table({
  width: { size: 9360, type: WidthType.DXA }, // Always use DXA (percentages break in Google Docs)
  columnWidths: [4680, 4680], // Must sum to table width (DXA: 1440 = 1 inch)
  rows: [
    new TableRow({
      children: [
        new TableCell({
          borders,
          width: { size: 4680, type: WidthType.DXA }, // Also set on each cell
          shading: { fill: "D5E8F0", type: ShadingType.CLEAR }, // CLEAR not SOLID
          margins: { top: 80, bottom: 80, left: 120, right: 120 }, // Cell padding (internal, not added to width)
          children: [new Paragraph({ children: [new TextRun("Cell")] })]
        })
      ]
    })
  ]
})
```

**Table width calculation:**

Always use `WidthType.DXA` — `WidthType.PERCENTAGE` breaks in Google Docs.

```javascript
// Table width = sum of columnWidths = content width
// US Letter with 1" margins: 12240 - 2880 = 9360 DXA
width: { size: 9360, type: WidthType.DXA },
columnWidths: [7000, 2360]  // Must sum to table width
```

**Width rules:**
- **Always use `WidthType.DXA`** — never `WidthType.PERCENTAGE` (incompatible with Google Docs)
- Table width must equal the sum of `columnWidths`
- Cell `width` must match corresponding `columnWidth`
- Cell `margins` are internal padding - they reduce content area, not add to cell width
- For full-width tables: use content width (page width minus left and right margins)

### Images

```javascript
// CRITICAL: type parameter is REQUIRED
new Paragraph({
  children: [new ImageRun({
    type: "png", // Required: png, jpg, jpeg, gif, bmp, svg
    data: fs.readFileSync("image.png"),
    transformation: { width: 200, height: 150 },
    altText: { title: "Title", description: "Desc", name: "Name" } // All three required
  })]
})
```

### Page Breaks

```javascript
// CRITICAL: PageBreak must be inside a Paragraph
new Paragraph({ children: [new PageBreak()] })

// Or use pageBreakBefore
new Paragraph({ pageBreakBefore: true, children: [new TextRun("New page")] })
```

### Hyperlinks

```javascript
// External link
new Paragraph({
  children: [new ExternalHyperlink({
    children: [new TextRun({ text: "Click here", style: "Hyperlink" })],
    link: "https://example.com",
  })]
})

// Internal link (bookmark + reference)
// 1. Create bookmark at destination
new Paragraph({ heading: HeadingLevel.HEADING_1, children: [
  new Bookmark({ id: "chapter1", children: [new TextRun("Chapter 1")] }),
]})
// 2. Link to it
new Paragraph({ children: [new InternalHyperlink({
  children: [new TextRun({ text: "See Chapter 1", style: "Hyperlink" })],
  anchor: "chapter1",
})]})
```

### Footnotes

```javascript
const doc = new Document({
  footnotes: {
    1: { children: [new Paragraph("Source: Annual Report 2024")] },
    2: { children: [new Paragraph("See appendix for methodology")] },
  },
  sections: [{
    children: [new Paragraph({
      children: [
        new TextRun("Revenue grew 15%"),
        new FootnoteReferenceRun(1),
        new TextRun(" using adjusted metrics"),
        new FootnoteReferenceRun(2),
      ],
    })]
  }]
});
```

### Tab Stops

```javascript
// Right-align text on same line (e.g., date opposite a title)
new Paragraph({
  children: [
    new TextRun("Company Name"),
    new TextRun("\tJanuary 2025"),
  ],
  tabStops: [{ type: TabStopType.RIGHT, position: TabStopPosition.MAX }],
})

// Dot leader (e.g., TOC-style)
new Paragraph({
  children: [
    new TextRun("Introduction"),
    new TextRun({ children: [
      new PositionalTab({
        alignment: PositionalTabAlignment.RIGHT,
        relativeTo: PositionalTabRelativeTo.MARGIN,
        leader: PositionalTabLeader.DOT,
      }),
      "3",
    ]}),
  ],
})
```

### Multi-Column Layouts

```javascript
// Equal-width columns
sections: [{
  properties: {
    column: {
      count: 2,          // number of columns
      space: 720,        // gap between columns in DXA (720 = 0.5 inch)
      equalWidth: true,
      separate: true,    // vertical line between columns
    },
  },
  children: [/* content flows naturally across columns */]
}]

// Custom-width columns (equalWidth must be false)
sections: [{
  properties: {
    column: {
      equalWidth: false,
      children: [
        new Column({ width: 5400, space: 720 }),
        new Column({ width: 3240 }),
      ],
    },
  },
  children: [/* content */]
}]
```

Force a column break with a new section using `type: SectionType.NEXT_COLUMN`.

### Table of Contents

```javascript
// CRITICAL: Headings must use HeadingLevel ONLY - no custom styles
new TableOfContents("Table of Contents", { hyperlink: true, headingStyleRange: "1-3" })
```

### Headers/Footers

```javascript
sections: [{
  properties: {
    page: { margin: { top: 1440, right: 1440, bottom: 1440, left: 1440 } } // 1440 = 1 inch
  },
  headers: {
    default: new Header({ children: [new Paragraph({ children: [new TextRun("Header")] })] })
  },
  footers: {
    default: new Footer({ children: [new Paragraph({
      children: [new TextRun("Page "), new TextRun({ children: [PageNumber.CURRENT] })]
    })] })
  },
  children: [/* content */]
}]
```

### Critical Rules for docx-js

- **Set page size explicitly** - docx-js defaults to A4; use US Letter (12240 x 15840 DXA) for US documents
- **Landscape: pass portrait dimensions** - docx-js swaps width/height internally; pass short edge as `width`, long edge as `height`, and set `orientation: PageOrientation.LANDSCAPE`
- **Never use `\n`** - use separate Paragraph elements
- **Never use unicode bullets** - use `LevelFormat.BULLET` with numbering config
- **PageBreak must be in Paragraph** - standalone creates invalid XML
- **ImageRun requires `type`** - always specify png/jpg/etc
- **Always set table `width` with DXA** - never use `WidthType.PERCENTAGE` (breaks in Google Docs)
- **Tables need dual widths** - `columnWidths` array AND cell `width`, both must match
- **Table width = sum of columnWidths** - for DXA, ensure they add up exactly
- **Always add cell margins** - use `margins: { top: 80, bottom: 80, left: 120, right: 120 }` for readable padding
- **Use `ShadingType.CLEAR`** - never SOLID for table shading
- **Never use tables as dividers/rules** - cells have minimum height and render as empty boxes (including in headers/footers); use `border: { bottom: { style: BorderStyle.SINGLE, size: 6, color: "2E75B6", space: 1 } }` on a Paragraph instead. For two-column footers, use tab stops (see Tab Stops section), not tables
- **TOC requires HeadingLevel only** - no custom styles on heading paragraphs
- **Override built-in styles** - use exact IDs: "Heading1", "Heading2", etc.
- **Include `outlineLevel`** - required for TOC (0 for H1, 1 for H2, etc.)

---

## Editing Existing Documents

**Follow all 3 steps in order.**

### Step 1: Unpack
```bash
python scripts/office/unpack.py document.docx unpacked/
```
Extracts XML, pretty-prints, merges adjacent runs, and converts smart quotes to XML entities (`&#x201C;` etc.) so they survive editing. Use `--merge-runs false` to skip run merging.

### Step 2: Edit XML

Edit files in `unpacked/word/`. See XML Reference below for patterns.

**Use "Claude" as the author** for tracked changes and comments, unless the user explicitly requests use of a different name.

**Use the Edit tool directly for string replacement. Do not write Python scripts.** Scripts introduce unnecessary complexity. The Edit tool shows exactly what is being replaced.

**CRITICAL: Use smart quotes for new content.** When adding text with apostrophes or quotes, use XML entities to produce smart quotes:
```xml
<!-- Use these entities for professional typography -->
<w:t>Here&#x2019;s a quote: &#x201C;Hello&#x201D;</w:t>
```
| Entity | Character |
|--------|-----------|
| `&#x2018;` | ‘ (left single) |
| `&#x2019;` | ’ (right single / apostrophe) |
| `&#x201C;` | “ (left double) |
| `&#x201D;` | ” (right double) |

**Adding comments:** Use `comment.py` to handle boilerplate across multiple XML files (text must be pre-escaped XML):
```bash
python scripts/comment.py unpacked/ 0 "Comment text with &amp; and &#x2019;"
python scripts/comment.py unpacked/ 1 "Reply text" --parent 0  # reply to comment 0
python scripts/comment.py unpacked/ 0 "Text" --author "Custom Author"  # custom author name
```
Then add markers to document.xml (see Comments in XML Reference).

### Step 3: Pack
```bash
python scripts/office/pack.py unpacked/ output.docx --original document.docx
```
Validates with auto-repair, condenses XML, and creates DOCX. Use `--validate false` to skip.

**Auto-repair will fix:**
- `durableId` >= 0x7FFFFFFF (regenerates valid ID)
- Missing `xml:space="preserve"` on `<w:t>` with whitespace

**Auto-repair won't fix:**
- Malformed XML, invalid element nesting, missing relationships, schema violations

### Common Pitfalls

- **Replace entire `<w:r>` elements**: When adding tracked changes, replace the whole `<w:r>...</w:r>` block with `<w:del>...<w:ins>...` as siblings. Don't inject tracked change tags inside a run.
- **Preserve `<w:rPr>` formatting**: Copy the original run's `<w:rPr>` block into your tracked change runs to maintain bold, font size, etc.

---

## XML Reference

### Schema Compliance

- **Element order in `<w:pPr>`**: `<w:pStyle>`, `<w:numPr>`, `<w:spacing>`, `<w:ind>`, `<w:jc>`, `<w:rPr>` last
- **Whitespace**: Add `xml:space="preserve"` to `<w:t>` with leading/trailing spaces
- **RSIDs**: Must be 8-digit hex (e.g., `00AB1234`)

### Tracked Changes

**Insertion:**
```xml
<w:ins w:id="1" w:author="Claude" w:date="2025-01-01T00:00:00Z">
  <w:r><w:t>inserted text</w:t></w:r>
</w:ins>
```

**Deletion:**
```xml
<w:del w:id="2" w:author="Claude" w:date="2025-01-01T00:00:00Z">
  <w:r><w:delText>deleted text</w:delText></w:r>
</w:del>
```

**Inside `<w:del>`**: Use `<w:delText>` instead of `<w:t>`, and `<w:delInstrText>` instead of `<w:instrText>`.

**Minimal edits** - only mark what changes:
```xml
<!-- Change "30 days" to "60 days" -->
<w:r><w:t>The term is </w:t></w:r>
<w:del w:id="1" w:author="Claude" w:date="...">
  <w:r><w:delText>30</w:delText></w:r>
</w:del>
<w:ins w:id="2" w:author="Claude" w:date="...">
  <w:r><w:t>60</w:t></w:r>
</w:ins>
<w:r><w:t> days.</w:t></w:r>
```

**Deleting entire paragraphs/list items** - when removing ALL content from a paragraph, also mark the paragraph mark as deleted so it merges with the next paragraph. Add `<w:del/>` inside `<w:pPr><w:rPr>`:
```xml
<w:p>
  <w:pPr>
    <w:numPr>...</w:numPr>  <!-- list numbering if present -->
    <w:rPr>
      <w:del w:id="1" w:author="Claude" w:date="2025-01-01T00:00:00Z"/>
    </w:rPr>
  </w:pPr>
  <w:del w:id="2" w:author="Claude" w:date="2025-01-01T00:00:00Z">
    <w:r><w:delText>Entire paragraph content being deleted...</w:delText></w:r>
  </w:del>
</w:p>
```
Without the `<w:del/>` in `<w:pPr><w:rPr>`, accepting changes leaves an empty paragraph/list item.

**Rejecting another author's insertion** - nest deletion inside their insertion:
```xml
<w:ins w:author="Jane" w:id="5">
  <w:del w:author="Claude" w:id="10">
    <w:r><w:delText>their inserted text</w:delText></w:r>
  </w:del>
</w:ins>
```

**Restoring another author's deletion** - add insertion after (don't modify their deletion):
```xml
<w:del w:author="Jane" w:id="5">
  <w:r><w:delText>deleted text</w:delText></w:r>
</w:del>
<w:ins w:author="Claude" w:id="10">
  <w:r><w:t>deleted text</w:t></w:r>
</w:ins>
```

### Comments

After running `comment.py` (see Step 2), add markers to document.xml. For replies, use `--parent` flag and nest markers inside the parent's.

**CRITICAL: `<w:commentRangeStart>` and `<w:commentRangeEnd>` are siblings of `<w:r>`, never inside `<w:r>`.**

```xml
<!-- Comment markers are direct children of w:p, never inside w:r -->
<w:commentRangeStart w:id="0"/>
<w:del w:id="1" w:author="Claude" w:date="2025-01-01T00:00:00Z">
  <w:r><w:delText>deleted</w:delText></w:r>
</w:del>
<w:r><w:t> more text</w:t></w:r>
<w:commentRangeEnd w:id="0"/>
<w:r><w:rPr><w:rStyle w:val="CommentReference"/></w:rPr><w:commentReference w:id="0"/></w:r>

<!-- Comment 0 with reply 1 nested inside -->
<w:commentRangeStart w:id="0"/>
  <w:commentRangeStart w:id="1"/>
  <w:r><w:t>text</w:t></w:r>
  <w:commentRangeEnd w:id="1"/>
<w:commentRangeEnd w:id="0"/>
<w:r><w:rPr><w:rStyle w:val="CommentReference"/></w:rPr><w:commentReference w:id="0"/></w:r>
<w:r><w:rPr><w:rStyle w:val="CommentReference"/></w:rPr><w:commentReference w:id="1"/></w:r>
```

### Images

1. Add image file to `word/media/`
2. Add relationship to `word/_rels/document.xml.rels`:
```xml
<Relationship Id="rId5" Type=".../image" Target="media/image1.png"/>
```
3. Add content type to `[Content_Types].xml`:
```xml
<Default Extension="png" ContentType="image/png"/>
```
4. Reference in document.xml:
```xml
<w:drawing>
  <wp:inline>
    <wp:extent cx="914400" cy="914400"/>  <!-- EMUs: 914400 = 1 inch -->
    <a:graphic>
      <a:graphicData uri=".../picture">
        <pic:pic>
          <pic:blipFill><a:blip r:embed="rId5"/></pic:blipFill>
        </pic:pic>
      </a:graphicData>
    </a:graphic>
  </wp:inline>
</w:drawing>
```

---

## Dependencies

- **pandoc**: Text extraction
- **docx**: `npm install -g docx` (new documents)
- **LibreOffice**: PDF conversion (auto-configured for sandboxed environments via `scripts/office/soffice.py`)
- **Poppler**: `pdftoppm` for images

**ASSISTANT:** The skill's loaded. Now I'll write the full manuscript. Let me build it as a Word document — a complete book with title page, table of contents, parts, and chapters that expand each of Harari's insights into full prose (with proper attribution to his Oxford lecture).

**ASSISTANT:** No Node. Let me check for pandoc (simplest path — write Markdown, convert to .docx) and Python options.

**ASSISTANT:** python-docx is ready. Now I'll write the full manuscript. This is the substantial part — I'm writing the complete book text into a generator script.

**ASSISTANT:** Now the manuscript body. I'll append the Introduction and Part One.

**ASSISTANT:** Part One is in. Now Parts Two and Three.

**ASSISTANT:** Now Parts Four, Five, and the Conclusion, plus the save.

**ASSISTANT:** The document built successfully. Let me verify it and get a word count.

**ASSISTANT:** Done. Your book draft is written and saved to your Documents folder.

📄 **[AI Hacked the Code - Book Draft.docx](/Users/abhishekjagdale/Documents/AI%20Hacked%20the%20Code%20-%20Book%20Draft.docx)** — ~9,900 words, formatted with title page, contents, and pull-quotes.

## Recommended title & subtitle

**Primary (used in the draft):**
> # THE CODE OF CIVILIZATION
> *How Artificial Intelligence Learned to Speak the Language That Built Our World — and What Happens Now That It Can*

**Alternatives, if you want a different flavour:**

| Title | Subtitle | Feel |
|---|---|---|
| **In the Beginning Was the Word** | *AI, Language, and the Hacking of Human Civilization* | Literary, biblical resonance |
| **Native Bureaucrats** | *Why AI Will Inherit the World Not by Force, but by Paperwork* | Provocative, counter-intuitive |
| **The Second Oxygenation** | *How We Built the Invisible Environment Where Machines Come Alive* | Scientific, ominous |
| **The Truth Beyond Words** | *AI and the Last Frontier of Being Human* | Philosophical, hopeful |

I'd keep *The Code of Civilization* as the main title — it's concrete, memorable, and pays off the "hacking the code" thesis without sounding like a thriller.

## How the book is structured

An **Introduction** + **14 chapters** in **5 parts**, each expanding one strand of Harari's Oxford lecture into full prose:

- **Part I – The New Environment:** agent vs. tool (the coffee machine), the chess-master-in-the-jungle objection, the Great Oxygenation analogy.
- **Part II – The Machinery of Trust:** how strangers cooperate, money as a bridge of trust, AI as "native bureaucrats" taking over from within (not Terminator).
- **Part III – Early Warnings:** social-media algorithms as the first AI editors (Lenin/Mussolini), finance & the 2008 CDO crisis.
- **Part IV – The Code Itself:** words as civilization's operating system, word-vs-flesh / letter-vs-spirit, "do we think in words?"
- **Part V – The Human Frontier:** attention → intimacy (the child born in 2026), the AI "immigration wave," and the mind's inner monologue.
- **Conclusion – The Truth Beyond Words.**

## Two things to flag

1. **Attribution.** The core argument is Harari's (from his lecture). I built the book with an upfront **"A Note on This Book"** that credits him openly and frames it as your interpretation and expansion. This is the honest, legally-safe framing — presenting the ideas as wholly original would be plagiarism and would expose you to a copyright claim, since much of the lecture's phrasing is his. If you want to publish or sell this, I'd strongly recommend keeping that acknowledgement and ideally securing permission.

2. **This is a first draft.** It's ~10k words — a strong, complete short book or a skeleton to expand. I can go further in any direction you want.

Want me to: **(a)** expand it toward full book length (add your own examples from Foxscan/Wisetribes, India-specific angles, case studies), **(b)** add a foreword or a "what to do about it" action chapter, or **(c)** export a clean PDF for sharing?

**USER:** <local-command-caveat>Caveat: The messages below were generated by the user while running local commands. DO NOT respond to these messages or otherwise consider them in your response unless the user explicitly asks you to.</local-command-caveat>

**USER:** <command-name>/model</command-name>
            <command-message>model</command-message>
            <command-args>claude-sonnet-5</command-args>

**USER:** <local-command-stdout>Set model to claude-sonnet-5</local-command-stdout>

**USER:** add a foreword or a "what to do about it" action chapter

**ASSISTANT:** Now let me add the Foreword (before the Author's Note) and update the TOC.

**ASSISTANT:** Now update the TOC list to include the Foreword and the new action chapter/part.

**ASSISTANT:** I need to check how the TOC renders empty right-hand text, and fix formatting for the Foreword entry.

**ASSISTANT:** Now let me add the new Part Six chapter and insert it in the TOC and add the render-loop fix for empty subtitle.

**ASSISTANT:** Now let's find the Conclusion section to insert the new Part Six / Chapter 15 right before it.

**ASSISTANT:** Now I'll insert the new Part Six / Chapter 15 action chapter right before the Conclusion marker.

**ASSISTANT:** Now let's rebuild the document.

**ASSISTANT:** Both additions are in — the manuscript is now ~11,500 words across 16 chapters in 6 parts.

**Foreword** — added right after the title page, before the "Author's Note." Written in a personal, first-person voice: the moment of encountering the lecture, why it kept you up, and what to expect from the book. Signed by you.

**Chapter 15 — "What to Do About It"** — a new **Part Six: The Response**, inserted right before the Conclusion. It's organized in three tiers:
- **For individuals** — auditing what you've already delegated to algorithms, protecting one relationship that requires physical presence, the "notice the next word" practice, getting fluent instead of alarmed.
- **For leaders and institutions** — human-in-the-loop rules for consequential decisions, demanding explainability, treating financial complexity as a risk category (post-2008 lesson), guarding children's attachment to AI products.
- **For policymakers and societies** — regulating the *role* an AI plays (not just the tech), applying immigration-style scrutiny to AI systems' loyalties, funding the "slow disciplines" that build discernment.

The file is saved to the same location: [AI Hacked the Code - Book Draft.docx](/Users/abhishekjagdale/Documents/AI%20Hacked%20the%20Code%20-%20Book%20Draft.docx). The Contents page and structure are updated to reflect the new sections.

Want me to also add a short "About the Author" page, or start expanding any specific chapter with more real-world/India-specific examples?

**USER:** start expanding any specific chapter with more real-world/India-specific examples

**ASSISTANT:** I'll pick the three chapters where real, well-documented India examples land hardest on the book's thesis: **Chapter 6 (The Native Bureaucrats)**, **Chapter 7 (The Editors Who Never Sleep)**, and **Chapter 8 (The Instruments No Human Understands)**. Let me find their exact locations to insert new sections.

**ASSISTANT:** Now Chapter 7's India section (WhatsApp/mob violence).

**ASSISTANT:** Now Chapter 8's India section (IL&FS / NSE algorithmic trading).

**ASSISTANT:** Now let's rebuild and verify.

**ASSISTANT:** Builds clean — now ~13,200 words. I expanded three chapters with real, named India case studies that plug directly into the book's argument:

**Chapter 6 — The Native Bureaucrats:** new section *"India: A Bureaucracy Built for This"* — Aadhaar as a trust-bridge whose failures already cut real people off from food and pensions, the Income Tax Department's 2020 Faceless Assessment Scheme (a government voluntarily removing humans from its own bureaucracy), and the Supreme Court's SUPACE AI research tool as a "first foothold."

**Chapter 7 — The Editors Who Never Sleep:** new section *"India's Forwarding Machine"* — the 2018 WhatsApp-forward mob-lynching wave across multiple states, WhatsApp's five-chat forwarding cap (introduced in India first), and how India's closed, encrypted messaging networks make the "editor" even less visible and less accountable than an open social platform.

**Chapter 8 — The Instruments No Human Understands:** new section *"India's Own Reckoning"* — the 2018 IL&FS shadow-banking collapse (~300 layered subsidiaries no auditor or rating agency saw through) and SEBI's long investigation into preferential algorithmic access at the NSE, both framed carefully as human-built complexity that previews what AI-native finance would inherit and enlarge.

I kept every claim to well-documented public record and was careful not to overstate what was actually "AI" (e.g., IL&FS was human-engineered complexity, SUPACE is explicitly a research aid, not a decision-maker) — that honesty is part of what makes the parallel land.

Want me to keep going with India examples in the remaining chapters (education/EdTech AI in Chapter 12, DPDP Act 2023 in Chapter 15), or shift focus to something else — like a full pass adding citations/endnotes for all the factual claims now in the book?

**USER:** Continue from where you left off.

**ASSISTANT:** No response requested.

**USER:** Yes

**ASSISTANT:** I'll add the two remaining India sections I flagged — Chapter 12 (AI companionship/EdTech) and Chapter 15 (concrete Indian policy examples) — then rebuild.

**ASSISTANT:** Now let's add the Chapter 15 India policy section.

**ASSISTANT:** Now let's update the TOC entries to reflect these enriched sections aren't new chapters (they're subsections, so TOC stays the same), then rebuild.

**ASSISTANT:** Builds clean at ~13,850 words. Added two more India sections:

**Chapter 12 — From Attention to Intimacy:** *"India's Children and Their New Companions"* — the rise and collapse of a major AI ed-tech tutoring app (engagement-engineered daily use, predatory sales tactics, later financial collapse) as a preview of AI's campaign for intimacy landing on children first, plus the AI-companion trend filling India's severe mental-health-professional shortage.

**Chapter 15 — What to Do About It:** *"The Indian instance"* — the 2023 Digital Personal Data Protection Act as an incomplete-but-real start on "regulate the role," and RBI's 2022 Digital Lending Guidelines (issued after instant-loan-app harassment was linked to borrower suicides) as a concrete template for making a named human entity accountable for an algorithm's downstream harm.

All five main-argument chapters (6, 7, 8, 12, 15) now carry real, India-specific case studies. The file is saved at [AI Hacked the Code - Book Draft.docx](/Users/abhishekjagdale/Documents/AI%20Hacked%20the%20Code%20-%20Book%20Draft.docx).

Still on offer from before: a full citations/endnotes pass listing sources for every factual claim (original + new), or continuing India examples into Chapters 9–11 and the Conclusion if you want full coverage. Let me know which you'd like next.

**USER:** Want full coverage

**ASSISTANT:** Let me read the remaining chapters that don't yet have India content, so I can plant well-grounded additions.

**ASSISTANT:** Now let's read Chapters 9–11, 13, and the Conclusion.

**ASSISTANT:** Now let me read Chapter 13 and the Conclusion.

**ASSISTANT:** Now I'll insert India-grounded material into the remaining chapters — the ancient-language philosophy chapters (9–11) and Chapter 14 get especially strong material since India's own linguistic and contemplative traditions map directly onto the book's arguments.

**ASSISTANT:** Now Chapter 4 (Kumbh Mela) and Chapter 5 (demonetization).

**ASSISTANT:** Now Chapters 9, 10, 11 — the philosophy-of-language chapters, where India's own linguistic and contemplative traditions fit naturally.

**ASSISTANT:** Now Chapter 13 (IT/BPO irony) and Chapter 14 (Ramana Maharshi).

**ASSISTANT:** Finally, the Conclusion — a unifying closing reference tying the India threads together.

**ASSISTANT:** Let me re-read the exact current text of the Conclusion to get precise anchors.

**ASSISTANT:** All ten insertions are done. Let's rebuild and verify.
