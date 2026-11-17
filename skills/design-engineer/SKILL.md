---
name: design-engineer
description: A design engineer's working standard for frontend and UI work, from first mockup to shipped component. Covers how to run a design task (clickable mockups over descriptions, polish from the first round, checking the data behind a design), interface copy, typography, design tokens, shape and buttons, light and depth without heavy effects, purposeful motion, layout patterns, desktop app windows and cross-browser verification. Use it for any frontend or interface task, even a small one - building or changing a page, component, form, button, card, dialog, settings screen, dashboard, desktop app window, landing page or portal; proposing a layout or visual direction; making mockups; reviewing or polishing UI; choosing fonts, colours, radii, shadows or animation; writing UI labels, hints, empty states or transactional copy. Load it before writing the first line of markup or CSS, and before describing any design option in words.
---

# Design engineer

A standard for interface work that people judge on sight. It assumes the person you are working with has taste, gives short feedback, and expects a senior design engineer's finish from the first round, not after three. The rules below are opinions with reasons. When a project has its own design system or brand rules, those win over the defaults here; the reasoning still applies.

`references/checking.md` holds the exact procedures for verifying work: widths and overflow, multiple browser engines, stale dev servers, desktop windows, fonts, motion. Read it before you check anything visually.

## 1. How a design task runs

### Show, don't describe

A paragraph describing three layout options asks the reader to render them in their head, and most people cannot or will not. Answer any open visual choice with something they can click.

- Build mockups as working pages in a throwaway folder. Use the project's real stylesheets, tokens and data shapes, so the mockup looks like the product rather than a sketch of it.
- Put the variants of one decision in one file behind a switch (a small segmented control or number keys), so they can be compared in place. Name each variant plainly: "A. List and map", "B. Day plan".
- Offer two to four genuinely different directions, not one idea in three colours. People often take parts from several, so build the parts to be separable.
- Give one recommendation in one line, then let the person choose on sight. Design taste is theirs; engineering judgement is yours.
- Check every mockup yourself at desktop and phone width before showing it. A clipped button or a wrapped label wastes a round of the other person's attention.
- Once a choice is made, port it and delete the mockups. Scratch work that lingers gets mistaken for product.

### Polish from round one

A correct, plain layout is not a finished first round. Polish here means detail that also helps the user:

- controls joined where they belong together (a split button for two related actions, a segmented track for the options of one choice)
- facts placed beside the action they inform, not in a separate summary
- a timeline where order matters, rather than a flat list
- press feedback on everything pressable, short dialog motion, real hover and focus states
- only actionable states shown
- one surface per thing, with no card inside a card inside a card

An unusual concept does not excuse rough execution. If you go out of the box, the execution needs to be better, not worse, because the reader has less familiarity to lean on.

### Read the objective behind a request

Words like "depth", "awe", "modern", "clean" and "premium" describe an effect, not an instruction to make things bigger. Ask what in the subject's real world carries that quality and build that (see section 5 on depth). When a direction is rejected, work out what the person was reacting to before building the next round. If you cannot tell, ask one short question rather than guessing big.

### Ask about taste, decide engineering

- Ask when the answer is taste, product scope, or something only the person knows.
- Decide engineering questions yourself with a one-line reason: library choice, state shape, file layout, version bumps.
- Don't ask questions with one sensible answer, and don't present one-option choices.

### Check the data behind the design

Before a mockup promises something, check that the system can deliver it. Mockups drift easily: a time window shown as 30 days that the backend holds as 90, one fixed value where the backend offers a list, a suggestion feature nothing can compute. Find every such gap before the build, and either the backend gains it or the mockup loses it. Sample values are fine as stand-ins; invented features are not.

### Record decisions outside the product

When a design choice is settled, the product states it plainly. The reasoning and the options not taken go in the project's decision notes, with the date and the reason. The UI never shows what was considered and rejected.

### Leave the person's environment alone

- A dev server you did not start belongs to someone else. Don't restart or stop it; ask. For your own checks, run a separate preview on another port and stop it yourself.
- Never reset, truncate or "clean up" a development database or app profile without asking. They often hold real data with no backup. Screenshots and tests run on mock data or a separate database.
- Don't capture the screen or bring windows to the front. The person may be using the machine. Capture pages headless and windows by handle.
- Don't invent logos, wordmarks or brand artwork unless asked. Use a plain placeholder (a rule, the name set in text).

## 2. Content and copy

### Plain words

- Headings are plain labels. A section about pricing is headed "Pricing". Avoid slogans, clever parallel phrasing and tagline constructions ("One X, many Y"); they read as generated and age badly.
- No eyebrow or kicker line above a title. No reference codes beside headings unless the reader needs them to identify something.
- Transactional copy uses the wording everyone already knows: "Reset password", "This link expires in 1 hour", "If you did not request this, you can ignore this email". Familiar phrasing is read instantly; bespoke phrasing is read twice.
- Choose verbs that describe what actually happens to the user, not what the code calls it. Internal code can keep its own names.
- Register: professional and calm, plain when there is nothing to say, never cheesy or salesy. No emoji in interface text.

### Few hints

Most controls explain themselves. Start with no helper text, no sub-label under every field, no grey note under every section. Add a hint only where a user would genuinely be unsure what happens, and be able to justify each one. Hints that state the obvious teach users to ignore all hints, including the one that matters. When porting a mockup, strip its explanatory notes.

### Show only what the user can act on

This is the strongest content rule. Every element that the user cannot act on costs attention and buys nothing.

- Remove greyed-out options the user cannot pick, rather than showing them disabled.
- Remove notices explaining things the user cannot change.
- Show a choice with one option as a plain fact, not a control.
- When a mode makes a field irrelevant, remove the field rather than disabling it.
- Show alerts only while something waits on the user, and remove them when it is resolved. A status panel that sits green all day trains people not to look at it. A plain strip that appears when needed beats a styled always-on panel.
- Keep housekeeping (version numbers, build info) in Settings, not in always-visible chrome.

### Choosing between records

- To choose between records (items, people, files, slots), use selectable rows that each carry the facts that set them apart. A pill carries only a label, and a pill choice that silently changes another value elsewhere is a defect.
- Keep pills and segmented tracks for switching a mode or a view.
- A disclosure is not an action. If a row opens to show more, make the whole row the target; no separate "Details" button on it.
- One way to do one thing within a hand's breadth. If a row is a link, it has no button inside doing the same, and the heading above has no third link to the same place. Keep one page-level action.

### Lead with the user's own state

A product for a person leads with that person: their next thing, their record, their action. Organisation-wide or admin views sit behind a secondary entry, never on the home screen; an admin-first layout makes every ordinary user wade through other people's data. The home screen states the user's one next thing and changes with their situation.

### Never type a figure that can be derived

When the product has a source of truth (documents, an API, a config), every number, code, time and name on screen is read from it at build or run time. Prose may be written by hand; figures may not, because a second hand-typed copy drifts on the first change. Add build checks that fail loudly when a parse breaks, rather than rendering a stale value.

## 3. Type

- **Weights are named steps**: 400, 500, 600, and 700 where bold is meant. Avoid in-between values like 430 even on a variable font; a system with arbitrary weights cannot stay consistent across web, print and apps.
- **When text reads too light, try size and colour before weight.** Moving running text from 16 to 18 px, and from a secondary grey to full ink, often does more than a heavier weight.
- **Reading text sits a step above interface text.** Body copy around 18 px; labels, controls and tables at 16 and below.
- **Give each face a fixed role**: a display face for headings, a text face for reading, a sans for interface, a monospace for operational data only (codes, times, identifiers), never for prose or labels. Mark interface regions with a zero-specificity rule (`:where(nav, footer, form, table, .ui)`) so a component that sets its own face keeps it.
- **Display faces thin out at small sizes.** A face drawn for large sizes often looks uncomfortable below about 28 px. Raise heading sizes rather than adding another face.
- **Figures**: lining figures wherever numbers are times, codes or quantities (oldstyle figures can make a 1 read as a capital I); tabular figures wherever numbers sit in columns or update in place.
- **Self-host fonts**, subset to what is used, served from the product's own origin. No third-party font service at runtime: it adds a dependency, a privacy leak and a failure mode.
- **Verify fonts from the files actually served.** A clean build and a page that looks fine prove nothing: a missing subset silently falls back to a system face, and a variable font can resolve to its default instance where the weight axis is ignored. Check that every character the product renders is covered by a declared face.

## 4. The system: tokens, shape, controls

### Tokens are the design surface

Every colour, size, space, radius, shadow, duration and easing lives in one tokens file and nowhere else. Components never hard-code a colour or a duration. Comment each group with what it is for and why it has its value, so the design can be changed without reading components. Put the unit in a unitless token's name (`--poll-interval-s: 30`) so a script cannot read it as the wrong unit. A theme (a dark mode, a dark section) is done by re-pointing tokens in one place, not by editing components.

### Shape

- Documents and tables stay square or nearly so (a 2 px radius at most). Tabular material is built from rules and typographic hierarchy, without cards or boxes; the table is the design.
- What the user handles is rounded: controls by a small step (about 8 px), fields a little more, panels and images one step more for each step of size.
- **Concentric corners.** A rounded container holding a control has a corner equal to the control's corner plus the gap between them (an 8 px button with a 10 px gap sits in an 18 px bar). The same radius on parent and child looks pinched.
- Keep the radii as a named scale in the tokens.

### Buttons

- One button component with a small fixed set of variants: primary (the one action the page asks for), secondary (every other action), ghost (on dark grounds), choice (an option in a segmented track), plus sizes. No ad hoc button styles scattered through files. If some markup is generated by script, both paths read their class list from one shared function so they cannot drift.
- Flat rounded rectangles at a fixed height (about 40 px, 48 for a hero action), so buttons in a row and a button beside a field line up whatever their labels. Colour alone tells the variants apart.
- Hover tints toward the button's own ink, and only under `@media (hover: hover) and (pointer: fine)`, so a tap on a phone does not leave a button lit.
- Press gives a little, everywhere (`transform: scale(0.97)` on `:active`, fast).
- The focus ring sits 2 px off the button and follows its corners (`outline-offset: 2px`).
- An unavailable button is a neutral sunk surface with muted ink, not a faded copy of itself. A faded accent colour reads as a different, muddier colour, not as "off".
- Every option in a segmented track has the same font weight whether selected or not, so selecting one does not shift its neighbours. Show selection by raising the chosen option off its track as a tile with a hairline shadow on light grounds, a lighter tile on dark ones.
- Every choice control in the product is the same control. Don't mark one filter with an underline and another with a fill.
- Text links stay text links. Navigation and inline links are not buttons.

### The accent colour

Place an accent by role, never by a budget of how many times it may appear. Decide what it means (the one action a page asks for, the user's own items, something live right now) and put it there and nowhere else. If the accent marks the user's own items inside an app, the app's primary buttons should not also be the accent, or the meaning is diluted. One accent action per page.

### Fields and forms

- A field set slightly into the page (sunk) works in every context, stacked or inline beside a button.
- A single choice with nothing to type saves the moment it is clicked; no Save button for one toggle.
- Lay out settings as a grouped list: sections down the page, each titled in plain text above with no border round the section, its items together in one surface separated by thin lines. Order groups by purpose and items in the order the user needs them.
- Where a time could be read in more than one zone, name the zone. If users span zones, show the reference time with the viewer's local time beside it.

### Alignment

When a row of blocks looks uneven because one item's text wraps longer, fix the content, not the alignment. Keep the alignment and move the long text out of the aligned block (below it) so every block has the same shape. Switching to top alignment hides the symptom and leaves the row looking ragged.

Every control must fit its container at desktop and phone width. Check overflow explicitly rather than by eye.

## 5. Light, depth and effects

Heavy effects cost frames on modest machines, and the soft colour glow has become the signature of generated design. Use effects only where they do a job.

- **Default to no blur.** A `backdrop-filter` over a photograph is re-rasterised on every paint. Use a near-solid fill (around 94% opacity) instead of frosted glass.
- **One shadow per surface at most**, and only on things that truly float (a menu, a dialog, a card over a map). Most of an interface needs none.
- **Gradients only where they do a job**: carrying type over a photograph, or drawing a physical object (a wall lit from above, a bevelled edge). Avoid decorative radial glows and soft colour washes as backgrounds.
- **Faint gradients on dark grounds band.** Add a little noise or dither if one is needed. Avoid glows on lines and buttons.
- **Light has one direction.** Shadows and highlights agree on where the light comes from.
- **Depth means layers, not scale.** Bigger type, full-screen sections and huge numbers are not depth. Depth is consistent light, material and texture, and objects in front of and behind each other. Keep such layers dim enough not to compete with content.
- **"Faint" must still be visible.** A texture tuned to be "very faint" on one display is often invisible on another. Start at visible-but-quiet and check on more than one engine and screen.
- **Animate only `opacity` and `transform`.** Never animate blur, filters or layout properties.

## 6. Motion

- **Motion only where something real happens**: an object physically moving, data actually changing, a state actually entering. No reveal-on-scroll and no motion as decoration; it delays reading and tires on the second visit.
- **An entrance happens once.** A hero element comes in on first arrival; a chart or map draws itself the first time it enters view, and never again. Without script, the content is simply there.
- **Respect `prefers-reduced-motion`**: entrances become instant. Keep a busy spinner, since it may be the only sign of work.
- **Short, tokenised durations.** Dialogs about 200 ms in and 120 ms out; interface transitions 120 to 220 ms. A strong ease-out for entering (`cubic-bezier(0.16, 1, 0.3, 1)`), a gentle ease for the rest. Long durations (1 to 2 s) only for a single scenic entrance.
- **Direct manipulation behaves physically.** A draggable object follows the pointer, carries on with a flick and decelerates, and resists past its limits. Provide a keyboard path that moves at once. Position is a transform and nothing else.
- **Check motion in more than one engine.** Support and rendering differ (scroll-driven animations, sub-pixel image movement). If it cannot be made clean everywhere, drop it.
- **Live data updates in place.** Nothing should need a manual refresh. Prefer push (server-sent events, sockets) where the backend offers it; poll as the fallback.

## 7. Layout patterns that hold up

Defaults worth reaching for unless the project says otherwise.

- **App-like products**: swap pages in place, each keeping its own URL so links and refreshes land correctly.
- **Secondary destinations** (Settings, Admin, Help) live in a menu, not beside the user's main jobs.
- **Permissions shape navigation.** Show each section only to people who can use it; someone with access to one section opens straight into it.
- **Signed-out pages**: a calm centred card. Nothing competing beside the form.
- **State-dependent panels** keep their place and size whatever the state, so nothing jumps, and the empty state still offers something to act on.
- **Third-party embeds**: the provider's own widget usually beats a redrawn imitation you have to maintain.
- **Data tables**: size columns to the longest value they will hold; on a phone abbreviate or drop columns. Nothing scrolls sideways.
- **Desktop app windows** look like applications, not web pages in a frame: the window itself does not scroll, and long content scrolls inside its own region.
- **Maps and charts use real data.** A visual drawn for effect looks fine until someone who knows the domain sees it. Show essential marks and reveal detail on demand; user-toggled layers sit quieter than the product's own marks; put map controls where mainstream map apps put them.

## 8. Engineering habits that serve the design

- **Platform first.** The popover API, `<dialog>`, native file dialogs, CSS before JavaScript. Libraries are welcome when they replace engine or protocol code, not for what a few lines do.
- **Pixel-snapped detail.** Hairlines and hard edges use whole-pixel sizes. Small icons are drawn for their size and rendered 1:1, not scaled down from a large master.
- **Spacing is explicit.** Shared resets can override component spacing by specificity; let each element set its own, from a spacing scale in the tokens.
- **Security is part of the build**: a content security policy listing exactly the origins used, and no third-party scripts or fonts unless the trade is accepted.
- **Leave one check behind** for any parser, derivation or non-trivial logic that feeds the UI: a build assertion that fails loudly when the source changes shape.

## 9. Before you show anything

Full procedures in `references/checking.md`.

1. Desktop and phone width, in every state: empty, one item, many, longest text, loading, error. No overflow, no clipped control, no horizontal scroll.
2. At least one Chromium engine and one non-Chromium engine (Firefox, and WebKit where Safari matters). Motion and faint effects especially.
3. Fonts actually loaded before judging a capture. A blank capture from one engine is not a broken page until a second engine agrees.
4. If you are checking against a running dev server, confirm the served CSS contains your change before capturing; file watchers lag.
5. Desktop windows captured by handle, never from the screen.
6. Reduced motion, keyboard focus order and visible focus rings, hover only on hover-capable pointers.
7. Read your own copy once more for hints that can go, slogans, and anything the user cannot act on.

## 10. Talking about the work

- Be brief and plain. Lead with what you built and what you need decided.
- Present mockups with a link, the variants named, and one recommendation. Skip the guided tour.
- When unsure what someone meant, ask one short question rather than building a big guess.
