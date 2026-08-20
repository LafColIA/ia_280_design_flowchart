# Research Design Chooser

An interactive rebuild of the course's two flowcharts — the **research design flowchart** (Week 9, Wed Oct. 21, *Brainstorming a research design*) and the **statistical causal inference chooser** (Week 5, Mon Sep. 21, *Causal analysis: observational data*) — merged into one tree. Built 2026-08-10.

`index.html` is the whole thing. One file, no build step, no backend, no network requests at load. It will run off a USB stick in a classroom with no Wi-Fi. (The only outbound links are the four Week 5 method videos, on the endpoint cards.)

Three ways in:

| Tab | Starts at | For |
|---|---|---|
| **Walk me through it** | topic selection | the full pipeline, question → design → analysis → data |
| **Just the causal method chooser** | "Is your IV binary?" | Week 5 and Lab 3, when they want the estimator, not the pipeline |
| **Text outline** | — | the tree as a nested outline, generated from the same data so it cannot drift |

A **live diagram of the whole tree sits under the question card** and updates as the student answers: filled boxes are the path taken, the outlined box is where they are, everything else is faded. It auto-scrolls to keep the current node in view, and zooms.

## Every design leads to its own analysis loop

The analysis question used to arrive only at the end, and only for observational work. Now each design is followed immediately by the choice of method appropriate to it:

| Design | Analysis loop | Ends at |
|---|---|---|
| RCT · lab · embedded survey experiment | on/off treatment, or several arms? | t-test + chart · regression across arms |
| Observational | the full causal chooser | t-test · matching · diff-in-diff · synthetic control · correlation · regression |
| Process tracing | counterfactual, or Bayesian? | counterfactual PT · Bayesian PT |
| Comparative cases | how many cases? then difference or commonality? | most similar · most different · QCA |

**For observational designs the design *is* the analysis**, and the app says so in as many words: picking difference-in-differences is not a thing you do after choosing a design, it is the choosing. Those milestones are flagged "Design and analysis" rather than one or the other.

The experimental branch deliberately does *not* re-ask whether the treatment was randomly assigned — the student answered that by designing the experiment. The only live question is the shape of the treatment, because a multi-arm experiment is not a t-test.

The case-study loops use the vocabulary from `AGENDA_07`: most similar and most different designs, and QCA gated behind a medium-N question so nobody picks it for a three-case project. **Necessary Conditions Analysis is deliberately excluded** — per Caleb, it is not really a case study approach, though `AGENDA_07` lists it alongside the others.

**There is no descriptive path.** Every question the app handles is causal; Caleb enforces that in class. An earlier build had a causal/descriptive gate and it has been removed.

## How the two charts were merged

The causal chooser attaches at the **design stage**, not at analysis. Caleb's call, and it is the right one: the chooser's questions ("do you have a control group with parallel trends observed over time?") are only answerable *yes* if you arranged for it during data collection. A student who first meets that question in April has already lost. The questions are worded to work in both tenses so the standalone tab still reads correctly in Week 5.

Three structural changes fell out of the merge:

**1. A causal/descriptive gate now sits before the design phase.** `AGENDA_10` already asks students to "decide on whether your question is descriptive or causal," so this is the course's own ordering. Descriptive projects skip the causal apparatus entirely and route to summary statistics and visualization. This replaced the old generic "do you need to control for confounders?" → regression-or-visualization step, which the causal chooser covers far better.

**2. ⚠ The two charts disagreed about single cases, and the main one structurally excluded synthetic controls.** The design flowchart sends "explaining a single event" to process tracing; the causal chooser sends "claims about a single event" to synthetic controls. But the design flowchart only *reaches* its single-event question after answering **no** to "is it feasible to collect data on a large number of cases?" — while a synthetic control needs exactly that: one affected case *and* many unaffected ones to compare against. As drawn, the two charts made synthetic controls unreachable from the single-case branch.

The fix, at Caleb's direction: the single-event branch now asks whether there are **reasonably comparable cases with data** running back to before the event. If there are, the student chooses between reconstructing the mechanism (process tracing) and estimating the magnitude (synthetic control), with a note that the two combine well. If there are not, process tracing as before.

**3. The lab / embedded-survey-experiment branch is corrected.** The node is *"Is there a high risk that the research conditions will affect the experimental outcome?"* with **Yes → lab experiment** and **No → embedded survey experiment** — confirmed by Caleb, and the opposite of what the first build inferred.

## Provenance

**The design flowchart's vector original is lost.** The `.gdraw` in `Class Agendas/` is a Drive streaming placeholder. The only surviving copy is a **903 × 344 px raster base64-embedded** in `source_materials/docs/AGENDA_10_brainstorming-research-design_FA25.md` — which is why that 38-line file is 120k tokens. Extraction snippet at the bottom of this file. Structure reads clearly at that size; some labels do not.

**The causal chooser came from `Causal Analysis In-Class Materials Backup.svg` / `.pdf`.** Its text is converted to outlines in the SVG, but the PDF carries real text, and **every node label was verified against it.** No inference was needed for this chart.

### Still unverified in the design flowchart

Two items from the original build remain read-by-inference. Neither is fatal, both are worth a glance:

- **The interview branch was restructured, not transcribed.** In the original, "sufficient resources for numerous in-person interviews?" has a **Yes** edge running down to structured interviews *and* a separate edge running up to the validity question, which cannot both be right. The app makes it a clean two-step.
- **The validity node contradicts its own edge labels** — it asks "external or ecological?" but the edges read *Ecological* and *Internal*. Normalized to ecological vs. internal.

One loose end in the causal chart, noted and ignored: a **stray "No" label** at the lower left of the Synthetic Controls box that nothing in the tree needs.

## The diagram

Laid out by a small layered engine in the page — no library, since nothing may be fetched at runtime. Ranks come from the longest path over forward edges, with back edges detected by a DFS colouring pass and routed around the left side. Columns aim for the mean of a node's parents, then de-collide.

**The flow runs top to bottom, and that was a measurement, not a preference.** Laid out left to right — matching the original charts — the canvas came out **5320 × 448**, a 12:1 strip, because the framing phase is nine single-node ranks in a row before the tree fans out at all. Vertical gives **2144 × 2041**, near-square, and scrolls the way people expect. If you want horizontal back, the change is small but the aspect ratio comes back with it.

Three things the first pass got wrong, all caught by measuring rather than looking:

- Back-edge labels rendered at **negative x**, off the canvas. The layout now reserves left margin for the back edges' loop before fixing the canvas width.
- Edge labels landed **on top of node boxes**. They now sit in the gap between two rows, where by construction no box can be.
- **Sibling labels printed over each other.** They are now packed side by side using each label's real width, so two branches of the same question can never collide.

Long choice labels get a short `mini` form for the diagram — "How big the effect was" reads as "Magnitude" on a chart. Anything after an em dash is guidance for the button, not a branch name, and is dropped.

## Verification

Checked programmatically in the browser after every change. Current state:

- **63 nodes**, zero broken links, zero unreachable, every node carrying a diagram label
- **all 1,316 distinct paths terminate** — 171 at "Sleep", 60 at the "I can't help from here" node
- exactly **two loop edges**, both deliberate (the "go read more literature" cycles)
- **every design reaches an analysis loop**, and all six statistical methods are reachable from the standalone causal tab
- the path Caleb asked for exists: `q_single → q_comparables → q_single_route → m_synthetic`
- diagram geometry: **zero box overlaps, zero labels over boxes, zero labels off-canvas, zero label collisions, zero truncated labels**

Re-run the walker any time by pasting it into the console; it derives everything from the `T` object, so it needs no updating when the tree changes.

**Caleb clicked through several paths and spot-checked the output on 2026-08-10 and signed it off.** The app is done; the only outstanding item is a URL.

## Editing

The whole tree is the `T` object at the top of the `<script>` block, one node per entry. Changing a question, a branch, or a course mapping is a one-line edit; the map view and the verification walker both read from it, so nothing else needs touching.

## Still open

- **No URL.** Following the R-lab convention it is named but not linked in the syllabus. It needs a host or the course website.

## Extracting the design flowchart image again

```powershell
python -c "
import re, base64
src = r'source_materials/docs/AGENDA_10_brainstorming-research-design_FA25.md'
m = re.search(r'data:image/(png|jpe?g);base64,([A-Za-z0-9+/=\s]+)', open(src, encoding='utf-8').read())
open('flowchart.' + m.group(1), 'wb').write(base64.b64decode(re.sub(r'\s', '', m.group(2))))
"
```
