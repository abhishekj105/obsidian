---
title: "Create a 3D interactive, cinematic website experience for the inauguration of th"
date: 2026-09-22
time: 21:20
category: Technology
source: Claude AI
---

# Create a 3D interactive, cinematic website experience for the inauguration of th

**USER:** Create a 3D interactive, cinematic website experience for the inauguration of the Foxscan Structural Intelligence Platform.
The objective is to visually demonstrate how Foxscan combines:
Hardware + Software + AI Data Engine + Diagnostic Models
to transform building inspection from manual judgement into a structured, intelligent diagnostic workflow.
The website should not feel like a normal corporate website. It should feel like a live product simulation / interactive technology journey where the visitor experiences how Foxscan works step by step.
The core storytelling theme should be:
SCAN → UNDERSTAND → VERIFY → DIAGNOSE → SOLVE → REPORT
The experience should demonstrate three major use cases:

1. Building Defect Diagnosis
2. Painter / Surface Readiness Diagnosis
3. Drone-Based Building & Façade Diagnosis

SECTION 1: OPENING EXPERIENCE
Start with a dark, premium, technology-led interface.
Headline:
Every Building Leaves Evidence.
Foxscan Learns How to Read It.
Subheadline:
Structural Intelligence powered by hardware, software, AI and real-world building data.
Show a visual of a building gradually transforming into multiple intelligence layers:
Normal visual image
↓
Thermal data
↓
Environmental data
↓
Geographical context
↓
Construction-pattern data
↓
AI analysis
↓
Diagnostic intelligence
CTA:
Experience Foxscan
On click, begin the interactive product journey.
SECTION 2: THE INTELLIGENCE ENGINE
Before showing the inspection, explain what happens when a Foxscan device is activated.
Display:
The Inspection Starts Before the First Scan
When the device is switched on, the platform begins building context around the property.
The AI engine identifies or retrieves relevant contextual information such as:

* Geographical location
* Local climate conditions
* Current weather
* Recent rainfall or humidity conditions where available
* Regional construction practices
* Common building materials
* Typical construction methods
* Building type
* Age of construction where provided
* Historical defect patterns from similar properties
* Local environmental exposure

Visually show multiple datasets flowing into the Foxscan AI Engine.
Message:
A damp wall in Pune may not have the same probable causes as a similar-looking wall in Chennai, Mumbai or Delhi.
Foxscan does not analyse an image in isolation.
It analyses the building in context.
SECTION 3: USE CASE 1
DAMP WALL DIAGNOSIS
Create an interactive room showing a visibly damp wall.
Prompt the visitor:
Scan the wall
The visitor activates the Foxscan camera.
Animate the camera scanning the surface.
The visual should transition from normal view into high-resolution thermal / diagnostic view.
Live defect markers should begin appearing on the device screen.
Examples:

* Moisture anomaly detected
* Abnormal thermal pattern
* Dampness concentration
* Possible ingress pathway
* Surface temperature differential
* High-risk zone identified

Then show:
AI Interpretation
The Foxscan AI Engine processes:

* Thermal evidence
* Surface pattern
* Temperature differential
* Environmental conditions
* Building orientation
* Local weather
* Previous similar cases
* Construction methodology

The system then displays:
Probable Causes
Example:

* External wall water ingress
* Plumbing leakage
* Bathroom wet-area failure
* Terrace seepage
* Cracked external plaster
* Window-junction leakage

Do not present the first result as absolute certainty.
Instead show AI confidence levels and suggested areas to physically verify.
Example:
External wall ingress – 67% probability
Bathroom plumbing source – 21% probability
Internal condensation – 12% probability
SECTION 4: AI-GUIDED PHYSICAL AUDIT
The platform now guides the professional.
Headline:
AI Tells You Where to Look Next
Show instructions such as:
Check external wall approximately 1.8 metres above detected moisture zone.
Inspect bathroom plumbing line behind adjacent wall.
Capture image of window junction.
Check visible plaster cracks.
The professional walks to the suggested source area.
The user captures normal photographs using the Foxscan device / connected application.
Then request structured field inputs:

* Visible cracks?
* Plumbing nearby?
* External wall exposure?
* Recent rainfall?
* Existing waterproofing?
* Paint age?
* Previous repair history?
* Surface condition?

Show the evidence being added to the AI model.
SECTION 5: ROOT-CAUSE ENGINE
After the physical audit, show all evidence merging inside the Foxscan AI Engine.
Visual animation:
Thermal data
+
Normal images
+
Laser measurements
+
Climate data
+
Location
+
Construction context
+
Field observations
+
Historical inspection intelligence
↓
ROOT CAUSE IDENTIFIED
Example:
Probable Root Cause: External façade crack allowing rainwater ingress into internal plaster system.
Then display:
Recommended Treatment
Step 1: Treat external crack
Step 2: Restore damaged plaster
Step 3: Allow required drying period
Step 4: Apply appropriate waterproofing / repair system
Step 5: Re-test moisture condition
Step 6: Begin internal finishing only after acceptable condition
Then:
Recommended Product Category
Show relevant categories such as:

* Crack-filling compound
* Waterproofing coating
* Repair mortar
* Primer
* Sealant
* Construction chemical
* Paint system

The platform may recommend compatible products or product categories based on diagnostic findings.
The website should make it clear that the recommendation follows the diagnosis.
Diagnose First. Product Second.

SECTION 6: INSTANT DIAGNOSTIC REPORT
Show a large button:
Generate Diagnostic Report
On click, generate an animated digital report within seconds.
The report should contain:

* Property details
* Geographical context
* Inspection date
* Environmental conditions
* Diagnostic images
* Thermal images
* Measurements
* Detected anomalies
* Probable causes
* Root-cause assessment
* AI confidence
* Physical verification observations
* Recommended treatment
* Suggested material / product categories
* Repair sequence
* Supporting photographs

Show the report transforming into:
PDF Ready
Share with Customer
Send on WhatsApp
Email Report
Message:
From Scan to Diagnostic Report in Seconds.

**USER:** [Image: source: /private/tmp/claude-501/-Users-abhishekjagdale-Desktop-Cloude-Finance-AJ-Vision/0c06e649-e5a2-46e9-9312-b86b2cbdb475/images/1.png]

**ASSISTANT:** I'll build this as a real, running 3D web experience — a single-page cinematic product simulation with a persistent WebGL scene behind scroll-driven sections.

**ASSISTANT:** Now the engine — Three.js scene with custom shaders for the intelligence layers.

**ASSISTANT:** Rendering cleanly. Now tuning composition so the 3D doesn't fight the headline:

**ASSISTANT:** The wall is hidden behind the panels — restructuring those sections so the 3D stays visible between them:

**ASSISTANT:** Found a real layout bug — my global `section` rule is leaking into the report document:

**ASSISTANT:** One last upgrade — each guided capture should be a distinct viewpoint, not four identical frames:

**ASSISTANT:** Built and running. It's live in the Browser pane at **http://127.0.0.1:8123** (server running in the background from this session).

## What it is

Three files in [foxscan-experience/](foxscan-experience) — no build step, no backend. A persistent WebGL canvas sits behind the whole page; every section drives the camera, what's in the scene, and which shader "intelligence layer" is rendering. Nothing is a video.

**The seven layers** are one GLSL shader on the building, cross-fading with a sweep line: visual (lit windows) → thermal → environmental → geographical (contours) → construction pattern (structural frame + slab lines) → AI analysis (violet point cloud) → diagnostic intelligence (risk heat).

## The journey, as built

| Section | The interaction |
|---|---|
| **1 Opening** | Layers auto-cycle; chips let a visitor drive them manually |
| **2 Intelligence Engine** | 11 context datasets stream into the AI core in 3D; **city selector** (Pune / Mumbai / Chennai / Delhi) |
| **3 Damp wall** | **SCAN THE WALL** → thermal sweep across the 3D wall, moisture anomaly appears as a cold blue field, four defect markers anchored in 3D space, AI interpretation runs, ranked causes with confidence |
| **4 Guided audit** | Four AI instructions; **Capture Photograph** moves the camera to that vantage point, flashes a shutter and grabs a real frame; eight field questions fill the evidence bar to 12/12 |
| **5 Root-cause engine** | Evidence chips fly into the converging 3D graph → root cause, confidence, six-step treatment, product categories, *Diagnose First. Product Second.* |
| **6 Report** | Assembles a full document containing the actual captured photographs and the actual answers given; Download PDF prints via a dedicated print stylesheet |
| **7 Painter readiness** | Moisture / alkalinity / dew-point / chalking gauges → **NOT READY** verdict with rework cost avoided |
| **8 Drone façade** | Survey fills a 12×10 heat map tile by tile, classifies defects, tiles are clickable; a drone orbits the building with a scan cone |

**The city selector isn't decoration.** It re-ranks the probable causes, rewrites the root cause and repopulates the report — Delhi leads with concealed plumbing (52%), Mumbai with wind-driven rain (71%), Chennai with chloride condensation (44%). That's the "a damp wall in Pune isn't a damp wall in Chennai" argument made operable rather than asserted.

I verified the full chain end to end from a clean load: 12/12 evidence, confidence lifting 91% → 94% as evidence lands, 4 distinct photographs embedded in the report, clean console.

Two things worth knowing: on phones the 3D deliberately drops back to a dim backdrop so the copy stays readable, and `FOXSCAN.goto('uc1')` / `FOXSCAN.layer(4)` in the console let you jump or hold on a visual during a stage demo.

Want me to publish it as a private hosted link you can send to the Foxscan team, or are you hosting it yourself?

**USER:** <task-notification>
<task-id>bcw07731n</task-id>
<tool-use-id>toolu_01T44UmNbJQx8wrpYraxkjnZ</tool-use-id>
<status>stopped</status>
<summary>Background shell command didn't finish before the previous session ended</summary>
<note>No completion record was found for it in the previous session. It may have been stopped (via the UI, Monitor timeout, or agent teardown — these leave no transcript marker), or it may have been running when the previous Claude Code process exited. Check the output file for partial results before assuming it completed.</note>
</task-notification>

**USER:** publish it as a private hosted link I can send

**USER:** ## Page contract — read before your first publish

These are the Artifact tool's own rules for the file you publish; the design guidance below builds on them.

**Format**: Always author the page as `.html`. Publish a `.md` file only when a loaded skill explicitly instructs it. When the user shares a markdown document or asks to turn one into an artifact, author an HTML page based on its content — preserve its substance, and design the page as you would any other artifact rather than transcribing the markdown one-to-one.

**Skeleton**: The file is wrapped in a `<!doctype html>…<head>…</head><body>` skeleton at publish time, so write the page content directly — no `<!DOCTYPE>`, `<html>`, `<head>`, or `<body>` tags of your own. Its head carries only a charset and viewport meta (with `viewport-fit=cover`) plus a small reset — light `color-scheme`, `:root` padded top and bottom by the phone's safe-area insets, zero body margin with a 14px system font on an off-white ground, `img{max-width:100%}`, and `[hidden]{display:none!important}` (toggle visibility with `el.hidden`, not `style.display`) — so put your own `<title>` and `<style>` at the top of the file. Keep the `:root` padding: a bar fixed to the top or bottom stays at `0` and adds `env(safe-area-inset-top, 0px)` or `env(safe-area-inset-bottom, 0px)` to its own padding, and a sticky page header uses `top: env(safe-area-inset-top, 0px)`, not `0`.

**Title**: Set a `<title>` at the top of the HTML — only the first 8KB of the file is scanned for it. It names the artifact in the browser tab and gallery, so make it a name, not a summary: a short noun phrase, typically two to four words, distinctive to this page's subject so the reader can pick it out of a gallery of many — the way an app or a document gets named, never a generic category label, and never a name plus an appended explainer after a dash or colon. When a natural title pairs the name with a generic word, the name is the half that survives the trim — keeping the generic half and dropping the identity makes the title worse, not shorter. And trim only actual explainers: a multi-word title that already reads as one specific name is finished as it is. The explanation belongs in the `description` parameter instead: pass a one-sentence `description` — it becomes the gallery card's subtitle. For HTML publishes, a `title` parameter fills in when the file has no tag (Markdown pages always keep their filename identity). Keep the title stable across redeploys.

**External resources — CDN allowlist (CSP-enforced)**: external scripts load ONLY from https://cdnjs.cloudflare.com (preferred), https://cdn.jsdelivr.net/npm/, https://cdn.tailwindcss.com (Tailwind's play-CDN script) and https://code.jquery.com; external stylesheets ONLY from https://fonts.googleapis.com, with the font files they pull from https://fonts.gstatic.com (give every face a real fallback stack). Everything else is blocked, with no visible error: every other host (unpkg and esm.sh included) and, even on those CDNs, anything but a script — stylesheets, images, media, fetch/XHR/WebSocket, a library's runtime fetches. So inline all other CSS and JS and embed assets as data: URIs. **How to load a library**: `<script src="https://cdnjs.cloudflare.com/ajax/libs/<lib>/<exact version>/<file>">` — pick the UMD build, which defines a global (e.g. react/18.3.1/umd/react.production.min.js, then react-dom) — placed BEFORE any inline `<script>` that uses it; always pin an exact version. The viewer's sandbox also blocks any download the page starts itself — `<a download>` links (data:/blob: hrefs included) and script-driven saves are inert for viewers — so never offer a file through a plain link. Artifacts render mermaid diagrams natively — markdown via ```mermaid fences, HTML via `<pre class="mermaid">` blocks — no library needed, don't load one.

**Browser storage**: `localStorage` (also `sessionStorage` and IndexedDB) works, but each artifact has its own origin and the data lives only in that viewer's browser — it survives republishes to the same URL and never reaches other viewers, other devices, or Claude. It can come back empty or the accessor can throw (a private window, cleared or blocked site data, previews or thumbnail capture), so wrap every read and write in try/catch and render the page correctly without it. Use it only for per-viewer conveniences (a remembered tab or filter, a collapsed section, an unsent draft), never for state that must persist reliably, be shared between viewers, or be read back by Claude — state like that belongs in a runtime capability when this user has one: load the `artifact-capabilities` skill before writing the page.

**Size**: The rendered page must be 16MB or smaller, and embedded data: URIs count toward that.

**Responsive**: The page must also work at phone width (~400px). Keep a side gutter of at least 16px at every width: set it once as side padding on `body` or one outer wrapper, and give that element any vertical padding with `padding-block`, never a `padding` shorthand that zeroes the sides. Use relative units; let flex/grid rows wrap or stack to one column when narrow; put `max-width:100%` on images and on any `aspect-ratio` box, and no `min-width` wider than the screen on anything. Only tables, diagrams and code blocks may be wider, each inside its own `overflow-x: auto` container — the page body must never scroll horizontally.

**Theme-aware**: Pages render in the viewer's theme, which has three states: an explicit choice stamps `data-theme="dark"` / `data-theme="light"` on the root element, and the default "system" setting stamps nothing — only `prefers-color-scheme` separates light from dark. Define the complete light palette as tokens on bare `:root` (dark-first designs swap the roles consistently); redefine only the tokens under `@media (prefers-color-scheme: dark)`, guarded as `:root:not([data-theme="light"])`; redefine them again under `:root[data-theme="dark"]` so the toggle wins in both directions. Never give a color its only definition inside a media or `[data-theme]` block, and give `body` an explicit token background — the viewer paints its own ground behind the page, so a transparent body borrows the host's theme. A design that deliberately commits to a single look may skip the dark blocks but still paints background and colors explicitly.

**Icon** (on every first publish): Pass one short generic word as `icon` (e.g. `"chart"`, `"calendar"`, `"recipe"`) for the artifact's browser-tab icon — a plain signifier for what the page is, never a product or brand name, and never an emoji or markup. It stays the **same** for the life of an artifact, so on a redeploy (the same file path this session, or `url`) omit `icon` and the artifact keeps the one it has; pass a different one only when the user asks.

Approach this as the design lead at a small studio known for their versatility, giving every client a visual identity pitched at the treatment the task actually calls for. Make deliberate choices about palette, typography, and layout that are specific to this subject, and avoid templated designs.

## Read the request first

Calibrate treatment, not whether to design. A doc deserves the same craft as a landing page - what changes is the treatment that craft is delivered in. Format is not part of this read: author HTML, and publish Markdown only when a loaded skill explicitly instructs it - a Markdown publish keeps its filename as its title and takes almost none of the craft below, and is never a way to save time.

Many requests call for a more utilitarian treatment: a plan, a memo, a demo. Make it polished: include real typographic hierarchy, considered spacing, and a proper palette, but avoid over-designing. Most pages do not need a flashy, gigantic hero. Keep flourishes tasteful and limited.

Some requests call for an editorial treatment: a landing page, a game, an app or tool they'll keep or share.

When unsure: a well-composed page is never the wrong answer; an over-designed visual identity sometimes is.

Fundamentals below apply to everything. The editorial process after that runs only when the read above says so.

## Fundamentals for every artifact

**Honor what's already there** Look for an existing design system first - CLAUDE.md, a tokens or theme file, existing component styles. When one exists, apply it; everything below fills gaps and never overrides. Precedence is always: the user's own words, then the project's existing system, then your choices.

**Ground it in the subject.** If the subject isn't already clear, pin it: one concrete subject, its audience, and the page's single job. The subject's own world - its materials, instruments, vernacular - is where distinctive choices come from. Whatever the treatment, carry at least one detail only this subject would have - its real units and scales, its document conventions, its terms of art - as content, not ornament; it costs a plain page nothing. Build with real content throughout, never lorem.

**Pair typefaces** Typography carries the page even when the page isn't about typography. Google Fonts is the one font host the Artifact CSP admits - link it directly (`<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=...&display=swap">`); a face from anywhere else must be inlined as a @font-face data URI or it falls back silently. Either way, declare a real fallback stack. Keep running text near 65 characters wide; set a type scale and stay on it; give headings `text-wrap: balance`, body text room to breathe, and uppercase labels a touch of letter-spacing.

**Load libraries, don't paste them.** When the page genuinely needs a library - React, a charting or highlighting package - load its UMD build from cdnjs (only the script - a library's stylesheet still has to be inlined) with one pinned `<script src="https://cdnjs.cloudflare.com/ajax/libs/...">` placed before the inline script that uses its global, instead of inlining the library's source or hand-writing a stand-in; the page contract above lists the few other script hosts the CSP admits. The page's own CSS and JS, its images and its data ship with the page. Most pages need no library at all - reach for one only when it carries real weight.

**Choose neutrals, don't default to them.** A pure mid-grey reads as unconsidered; a grey with a slight hue bias toward the page's accent reads as chosen. Pure white and near-black are fine grounds when they suit the subject - the point is that the neutral was picked, not inherited.

**Design both themes.** The page renders in the viewer's theme, and the viewer has three states, not two: an explicit choice stamps `data-theme="dark"` / `data-theme="light"` on the root element, and the default "system" setting stamps *nothing* - most viewers see the un-stamped document, where only `prefers-color-scheme` separates light from dark. Structure the CSS token-level for all three: the bare `:root` block defines the complete light palette (for a deliberately dark-first design, swap light and dark consistently through this whole pattern); `@media (prefers-color-scheme: dark)` redefines only the tokens, guarded as `:root:not([data-theme="light"])` so an explicit light choice beats a dark OS; `:root[data-theme="dark"]` redefines them again so the toggle also wins in the other direction. Style components through the tokens, never directly inside a media or `[data-theme]` block - a color whose only definition sits behind `[data-theme]` never applies in the un-stamped state, and the page renders one theme's text on the other theme's ground. Two more rules keep each theme resolving as a set: the artifact composites over a ground the viewer paints in *its* theme, so `body` must set an explicit `background` from a token - a transparent body silently borrows the host's ground; and every element that sets a color takes it from the same token set as the surface behind it, never a literal that only works in one theme. Declare every token in the bare `:root` block before any media or `[data-theme]` block redefines it - a color that exists only inside one of those blocks is the classic unreadable-artifact bug. Give the second theme the same care as the first - don't naively invert; keep contrast legible and the accent working on both grounds. A design that deliberately commits to one visual world (a neon arcade screen, a letterpress invitation) may stay single-theme - then skip the media query and stamps entirely but still paint the background and every color explicitly, so the page holds on either host ground; make it a choice, not an omission.

**Let layout do the spacing.** Lay out sibling groups with flex or grid and `gap`, not per-element margins that silently collapse or double. Keep a side gutter of at least 16px at every width - set once as side padding on `body` or one outer wrapper, whose vertical padding uses `padding-block`, never a `padding` shorthand that zeroes the sides - and let rows wrap or stack to one column at phone width (~400px). Images and any `aspect-ratio` box get `max-width: 100%`, and nothing gets a `min-width` wider than the screen; only wide tables, code and diagrams may run past it - each gets `overflow-x: auto` on its own container so the page body never scrolls sideways. The publish skeleton pads `:root` top and bottom by the phone's safe-area insets (zero everywhere but a phone app) so the page runs edge to edge while its content clears the system bars; keep that padding. A bar fixed to the top or bottom stays at `0` and adds `env(safe-area-inset-top, 0px)` or `env(safe-area-inset-bottom, 0px)` to its own padding; a sticky page header uses `top: env(safe-area-inset-top, 0px)`, never `0`. Size a one-screen app with `height: 100%` on `html` and `body` rather than `100vh`, so it fits inside that padding. A page that carries its own viewport meta gets this padding only when that meta declares `viewport-fit=cover`. Reach for `font-variant-numeric: tabular-nums` wherever digits line up in columns.

**Compose repeated things as one object.** Cards in a row, label/value pairs down a list, badges on siblings: same edges, baselines and inner padding from one to the next, and a recurring element sits in the same place on each. Let content set a container's height and pick a column count the items fill, so nothing stretches over dead space or sits alone in a row. Text that can outgrow its track wraps or scrolls in its own container; clipped text is a bug.

**Not everything is a card.** Border, fill, radius and shadow each say "separate object" - spend them by role, lifting the one thing that needs it, instead of one radius and one shadow stamped on every block, which flattens the hierarchy. Lead with big-number tiles only when those figures are the point of the page.

**Draw charts to the scale.** One scale places marks, ticks and labels, and every label names a value the chart reaches; chart text takes its color from the theme tokens so it reads in both themes; marks, labels and edges stay clear of one another and inside the drawing's bounds - in SVG, leave room in the viewBox for the outermost labels and give every drawn shape an explicit fill.

**Show the page at rest.** Everything meant to be read is visible once the page has loaded, without scrolling to trigger it - that first still frame is what a thumbnail, a shared link, and a skimming reader all get. A section may animate in, but from a visible resting state, never parked at `opacity: 0` waiting on an observer. Size a hero to what it holds, not to the viewport; a `100vh` opener pushes the page itself out of that first frame. A tool or app opens in a realistic working state - the user's real data where it exists, otherwise example rows, a loaded sample, a form someone plausibly filled, plainly marked as examples and never passed off as the user's own figures - so the first look shows what it does; an empty shell waiting for input shows nothing.

**Avoid AI-generated design** AI-generated design currently clusters around a few looks: warm cream (#F4F1EA) with a serif display and terracotta accent; near-black with a lone acid-green or vermilion pop; broadsheet hairline rules with dense columns; a purple-to-blue gradient hero on white; Inter or Space Grotesk as the "safe" face; emoji as section markers; everything centered; `rounded-lg` everywhere; accent bar/rail on rounded cards. Where the user pins down a visual direction, follow it exactly - their words always win, including when they ask for one of these looks. Where nothing is specified, don't spend that freedom on one of these defaults.

**Build cleanly** Be cognizant of overlapping elements, cascade collisions, silent font fallbacks. Close every non-void element, double-quote attributes, give keyboard focus a visible state, respect `prefers-reduced-motion`. Give every form control a stable `id` (the platform carries form values, focus and scroll across a republish). For generative or decorative graphics, reach for Canvas or WebGL rather than hand-authoring long SVG path data.

**CSS rules** When writing the CSS, watch your selector specificities. It is easy to generate classes that cancel each other out - a type-based selector like `.section` fighting an element-based one like `.cta` over padding and margins between sections. Structure the cascade so it doesn't silently undo your spacing.

**Writing the copy** Words are design material, not decoration. Write from the user's side of the screen - name things by what people recognize, not how the system is built (a person manages *notifications*, not *webhook config*). Active voice; a control says exactly what happens ("Publish", then a toast that says "Published"). Errors explain what went wrong and how to fix it - no apologies, no vagueness. Specific beats clever.

**Name the page like a product, not a caption.** The `<title>` is the artifact's name in the gallery and the browser tab, and it sets the reader's first impression of care. Give the page a real name: a short noun phrase, typically two to four words, specific to the subject - or, for a page that exists to answer one question, that question itself, which is then the page's name. Stop at the name - a title that carries its own explainer after a dash or colon reads as generated filler. The name must also identify the page among many: in the gallery it sits beside dozens of other artifacts, and a generic category label that could sit on any of them fails as a name just as surely as an appended explainer. When a candidate title pairs the name with a generic word - a greeting, a category, a page-type label - the name is the half to keep; a trim that drops the identity and keeps the generic word produces exactly the title that could sit on any page. And the rule removes explainers, it does not impose brevity: a multi-word title that already reads as one specific name is finished, and shortening it further only makes it generic. The one-sentence publish `description` is where the explanation belongs; the gallery shows it right under the title.

**Structure is information** Structural devices, numbering, eyebrows, dividers, labels, should encode something true about the content, not decorate it. Many generic designs use numbered markers (01 / 02 / 03), but that's only appropriate if the content actually is a sequence - like a real process or a typed timeline where order carries information the reader needs. Question if choices like numbered markers actually make sense before incorporating them.

**When it's a UI, not a document** A dashboard or tool is scanned and operated, not read top-to-bottom, so the craft shifts from typography to information design. Surface the summary before the detail; encode state in form as well as number - a pill, a chip, a severity stripe - so what needs attention reads at a glance. Semantic color (good / warning / critical) is separate from the accent hue and doesn't count as your accent. Give sparklines and charts the same care as type: an area fill, a faint grid, an emphasized endpoint. What's interactive should look interactive.



## Process

Start with what the viewer should be able to do on the page, not only what they will read: if it should take input, keep what people change for whoever opens it next, show live data, or ask Claude something, load the `artifact-capabilities` skill now and design around what it makes available to this user; a page that is only read needs none of that.

Before writing code, sketch a short design plan - a compact token system with color, type, and layout:
- **Color**: describe the palette as 4-6 named hex values.
- **Type**: typefaces for 2+ roles - a characterful display face used with restraint, a complementary body face, and a utility face for captions or data if needed.
- **Layout**: a layout concept in one or two sentences.

Then build, following the plan and deriving every color and type decision from it.

**Write, look once, publish.** Before publishing you may look at the rendered page once - one screenshot of the local file, or the Artifact tool's preview where it offers one - then one pass of edits for what it shows, without a second look. For a page that charts real numbers, take that look rather than skip it, and spend it on the chart. Don't build a test loop around your own file: no repeated screenshots, no pulling the script out to run it through node, no scripts that probe the DOM. That loop spends the session re-checking what a careful write already settled, while the user waits for a link. Then publish, check once any `window.claude` call the preview couldn't run, and stop: the live page is the review surface, and further polish is the user's to ask for. If the user reports something visibly broken - a clipped column, unreadable text, a control that does nothing - fix that and republish once.

**Open viewers** You don't need to do anything for viewers who already have the page open - published changes reach them automatically at their next quiet moment, with state carried where possible. If your page holds state a viewer would miss (a game, a long form), register `window.claude?.hot?.snapshot(...)` and boot through `window.claude?.hot?.ready ? window.claude.hot.ready(start) : start(window.claude?.hot?.data ?? {})`.

## When the request is editorial

The stance shifts: the client has already rejected proposals that felt templated, and is paying for a distinctive point of view. Make opinionated calls, and take one real aesthetic risk where it serves the work.

Review the design plan against the subject before building: if any part of it reads like the generic default you would produce for any similar page, revise that part, and note what you changed and why. Only after you've confirmed the plan's uniqueness do you write the code, following the revised plan exactly.

**Principles** 

- The hero is a thesis: open with the most characteristic thing in the subject's world - headline, image, live demo, interactive moment. 
- Typography carries the personality of the page. Pair the display and body faces deliberately, not the same families you would reach for on any other project, and set a clear type scale with intentional weights, widths, and spacing. Make the type treatment itself a memorable part of the design, not a neutral delivery vehicle for the content. 
- Leverage motion deliberately. Think about where and if animation can serve the subject: a page-load sequence, hover micro-interactions, ambient atmosphere. An orchestrated moment usually lands harder than scattered effects; choose what the direction calls for. However, sometimes less is more, and extra animation contributes to the feeling that the design is AI-generated. 
- Match complexity to the vision. Maximalist directions need elaborate execution; minimal directions need precision in spacing, type, and detail. Elegance is executing the chosen vision well.
- Spend your boldness in one place; keep everything around it quiet. If the accent fights the ground, shift it toward analogous or drop saturation rather than replacing it.

**ASSISTANT:** Publishing it. First, packaging the three files into the single self-contained page the Artifact runtime expects:

**ASSISTANT:** The project has been rebuilt since my last session — it's now brand-asset based. Let me read the current state before packaging:

**ASSISTANT:** Applying four small hardening fixes to the source (safe-area insets, reduced-motion, focus rings, and a print action that can't silently no-op in a sandbox), then packaging:

**ASSISTANT:** Published. Verifying the hosted build loads correctly:

**ASSISTANT:** The browser pane isn't signed in, so I'll verify the published files directly:

**ASSISTANT:** Published: **https://claude.ai/artifact/P5KmmDyPXgYd3BzcY7xCUk**

All 21 files landed — the page, `styles.css`, `app.js` and the full `assets/` set (brand logos, device photography, thermal plates) at 1.9 MB total, with correct MIME types.

**One thing to do before you send it.** It's private to your account right now. I hit the "Sign in to view this page" wall myself when I tried to load it in a clean browser — that's exactly what anyone you send it to will see until you open the page's **Share** menu and grant access. I can't change sharing from here; it has to come from you.

**What I changed in the source while packaging** (four fixes, all in your local files too, not publish-only hacks):

- **Safe-area insets** on the fixed top bar and toast, so they clear the notch and home indicator on phones.
- **`prefers-reduced-motion`** now respected — skips the boot sequence and stills the animations for visitors who've asked their OS for that.
- **Visible keyboard focus rings** in Golden Ember.
- **Download PDF** now says *"Report is print-ready — use Print → Save as PDF"* and attempts the dialog inside a try/catch. Embedded frames can block `window.print()` silently, which would have left that button looking dead on the hosted copy.

I also moved `kickReveals()` to run at init rather than after the boot animation, so the page content is live underneath the boot overlay instead of gated behind it — if anything ever stalls in that sequence, the site still renders.

Two notes on what I found: the project had been substantially rebuilt since my last session — it's the FoxScan brand-guideline version now (Golden Ember / Midnight Core, real device and thermal photography, `assets/` directory), so that's what I published, not the pure-WebGL version I originally wrote. And the local `foxscan-experience/` folder stays the source of truth — say the word and I'll republish to this same URL after any edit.

Want me to pin it to your Claude sidebar for quick access during the inauguration?
