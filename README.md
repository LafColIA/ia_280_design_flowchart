# Research Design Chooser — IA 280

**→ [lafcolia.github.io/ia_280_design_flowchart](https://lafcolia.github.io/ia_280_design_flowchart/)**

An interactive version of the course's two flowcharts — the **research design flowchart** (Week 9, *Brainstorming a research design*) and the **statistical causal inference chooser** (Week 5, *Causal analysis: observational data*) — merged into one tree.

Three ways in:

| Tab | Starts at | For |
|---|---|---|
| **Walk me through it** | topic selection | the full pipeline, question → design → analysis → data |
| **Just the causal method chooser** | "Is your IV binary?" | Week 5 and Lab 3, when you want the estimator, not the pipeline |
| **Text outline** | — | the whole tree as a nested outline |

A live diagram of the tree sits under the question card and updates as you answer: filled boxes are the path you took, the outlined box is where you are.

Every design leads to its own analysis loop — experiments to a t-test or a regression across arms, observational work to the full causal chooser, process tracing to counterfactual or Bayesian, comparative cases to most similar / most different / QCA. For observational designs the design *is* the analysis, and the app says so.

## Running it

`index.html` is the whole thing. One file, no build step, no backend, no network requests at load — it will run off a USB stick in a classroom with no Wi-Fi. The only outbound links are the four Week 5 method videos on the endpoint cards.

To edit: the tree is the `T` object at the top of the `<script>` block, one node per entry. Changing a question, a branch, or a course mapping is a one-line edit; the diagram and the text outline both read from it, so nothing else needs touching.

Build notes, provenance, and verification records live with the course materials, not here.
