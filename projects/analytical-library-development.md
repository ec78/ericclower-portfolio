---
title: "From Research Code to Usable Software: Modernizing Analytical Libraries with AI-Assisted Development"
description: "Turning research implementations into validated, documented GAUSS libraries with intuitive APIs, using AI-assisted development with human review and technical judgment."
layout: default
---

# From Research Code to Usable Software: Modernizing Analytical Libraries with AI-Assisted Development

## The challenge

A lot of valuable analytical work lives in research code. A method gets published, someone writes an implementation to produce the paper's results, and that code circulates among people who already understand it. It works, but it usually isn't ready for broad use: inputs are positional and easy to get wrong, outputs are hard to read, edge cases are untested, documentation is thin, and the code may predate current platform features.

Closing that gap is product work as much as engineering work. Before a researcher will rely on a library, it needs an API that makes sense, results they can verify, examples that match how they actually work, and an installation path that doesn't require reading the source.

---

## My role and scope

I independently develop and maintain analytical software libraries for GAUSS, separate from my role at IMPLAN. Some of these libraries are hosted in Aptech's GitHub organization or list Aptech Systems as publisher; the development work is my own. On these projects I lead the development work end to end:

- Evaluating analytical requirements and any existing implementation
- Software architecture and API design
- Integrating, expanding, and modernizing functionality
- Testing, validation, and reproducibility
- Documentation, examples, and AI-assistant guides
- Packaging, release preparation, and ongoing maintenance

Some of these libraries build on foundational code written by others. Where that's the case, the original authors are credited below and in each repository, and my work is the integration, expansion, modernization, validation, and release.

---

## How I approach these projects

**Start with how the library will be used.** I look at the workflow a researcher follows (diagnostics, estimation, post-estimation, reporting) and design the library around that sequence rather than around individual functions.

**Treat the API as the product.** Consistent control and output structures, sensible defaults, formula strings instead of manual column selection, and printed results that are readable without post-processing. A correct estimator with a confusing interface still doesn't get used.

**Validate against independent references.** Matching another implementation on real data is the clearest way to show that a library is correct. Where no direct reference exists, I say so and describe what was checked instead.

**Be honest about maturity.** If parts of a library are experimental, the documentation should say which parts and why.

**Write documentation for people and for AI assistants.** Most AI models have seen little GAUSS code, so I include guides and context files that help an assistant give accurate answers about the library.

---

## AI-assisted development

I use AI-assisted development throughout these projects: implementation, refactoring, debugging, test writing, documentation, and iteration. In practice that means:

- **Clear, durable context.** Each repository keeps a project context file (`CLAUDE.md`) describing architecture, conventions, data structures, and GAUSS language specifics, so the assistant works from the same facts each session. On longer projects, a separate status file tracks current work. I've written about this approach in [The Token Problem Isn't How Much Context You Use. It's What's In It.](https://medium.com/@eclow/the-token-problem-isnt-how-much-context-you-use-it-s-what-s-in-it-7df2a6dc351f)
- **Validation as the gate.** AI-generated code is not assumed to be correct. Results are checked against independent references, test suites, and published data.
- **My decisions.** Software design, technical trade-offs, what ships, and what is labelled experimental are my responsibility, not the assistant's.

AI makes it practical for one person to take on more of the full lifecycle: broader test coverage, more complete documentation, more careful modernization. It doesn't replace knowing whether an estimator is right.

---

## Featured example: dccelib

**Repository:** [github.com/ec78/pddcce](https://github.com/ec78/pddcce)

dccelib is a GAUSS library for panel data with cross-sectional dependence, where unobserved common factors such as business cycles or commodity shocks make residuals correlated across units and standard panel estimators unreliable. It implements the Common Correlated Effects (CCE) framework of Pesaran (2006).

### Starting point

The library originated with GAUSS code for the CCE estimator written by Takashi Yamagata (2008). I first restructured that code into a library in 2023. In 2026, I expanded and modernized it into the current release.

### What I built

- **Estimators:** Mean Group, CCE Mean Group, Dynamic CCE Mean Group, and a principal-components variant (PC-CCE-MG) for cases where the cross-sectional averages are near-collinear.
- **Diagnostics:** cross-sectional dependence (CD) test, CIPS panel unit root test, slope homogeneity tests, a CCE rank condition check, and the Westerlund panel cointegration test.
- **Post-estimation:** half-panel jackknife bias correction, wild bootstrap standard errors, long-run multipliers, diagnostic plots, and LaTeX table export.
- **Options for harder cases:** pooled CCE, an I(1) extension, and two-way factor structures.

### Modernization

Modernizing the existing code surfaced real defects. Among the bugs fixed: an operator-precedence error in automatic lag selection that computed T/3 instead of T^(1/3), and a lag-construction loop that only ran once. Other changes included computing matrix inverses once instead of repeatedly, consolidating three separate print routines into one, and removing a legacy interface that returned 11 positional values.

### Developer experience

- A control structure (`mgControl`) with documented defaults and a single output structure (`mgOut`) shared by every estimator
- A formula-string interface, for example `"log_rgdpo ~ log_ck + log_ngd + csa(log_hc)"`, with cross-sectional-average variables declared inline
- Installation through the GAUSS Application Manager
- Ten example scripts, from single estimators through a complete recommended workflow
- A full [README](https://github.com/ec78/pddcce#readme), an API reference, and an [LLM guide](https://github.com/ec78/pddcce/blob/main/docs/llm_guide.md) with a context block users can paste into an AI assistant

### Validation

The three primary estimators (MG, CCE-MG, and DCCE-MG) match R's `plm::pmg()` to six decimal places on Penn World Tables data. PC-CCE-MG has no direct R equivalent, so it is checked by confirming convergence to CCE-MG as the number of principal components approaches full rank. The validation scripts are in the repository, so the results can be reproduced.

### Attribution and license

The original CCE code is by Takashi Yamagata. Per the repository, dccelib is published by Aptech Systems under the GAUSS Standard License (non-commercial public use).

---

## More libraries

The same approach applies across other GAUSS libraries I develop and maintain.

### QARDL for GAUSS

**Repository:** [github.com/aptech/gauss-qardl](https://github.com/aptech/gauss-qardl)

- **Starting point:** original GAUSS code by Jin Seo Cho for quantile autoregressive distributed lag (QARDL) estimation, based on Cho, Kim, and Shin (2015).
- **What I built:** all of the library's development beyond the original code, including updating it to use GAUSS 26 features such as `quantileFit`. That work added structures and modern workflow helpers, robust and HAC inference, ARDL, NARDL, and CS-ARDL model families, integrated full-workflow functions, prediction and forecasting, bootstrap tools, and formula-string support.
- **Quality and documentation:** a release test suite, versioned releases, citation files, and an `llms.txt` file for AI assistants.
- **License:** MIT, hosted in Aptech's GitHub organization.

### QUAIDS for GAUSS

**Repository:** [github.com/ec78/gauss-aids-model](https://github.com/ec78/gauss-aids-model)

- **Starting point:** my own first version of GAUSS demand-system code from 2019, rebuilt as a full library in 2026.
- **What I built:** estimation of linearized AIDS, iterated AIDS, and QUAIDS demand systems with instrumental-variables treatment of total expenditure, restriction tests, elasticities, diagnostics, survey-data tools, and optional extensions.
- **Validation:** cross-validated against an independent R implementation (`micEconAids`) on published data, and against known-true synthetic data.
- **Honest maturity labels:** the library is a public alpha. Its README separates **stable** components from **experimental** ones and publishes measured convergence-failure rates for the experimental models, so users know where to be careful.
- **Release process:** automated tests in continuous integration, a scripted release gate, and citation metadata.
- **Writing:** [The Almost Ideal Demand System](https://medium.com/@eclow/the-almost-ideal-demand-system-65b9bcf2b1c0) explains the method, using estimates produced with this library.
- **License:** MIT.

---

## In development: GAUSS MCP Server

I'm developing a Model Context Protocol (MCP) server for GAUSS, to explore how AI assistants can work with GAUSS through structured tools and workflows.

**The problem it explores:** established analytical platforms have deep capabilities but steep learning curves. AI assistants could make them more accessible, but only if the interface keeps users in control and keeps the analysis transparent, reliable, and technically correct. Statistical work can't be a black box.

**What it connects:** analytical software, AI integration, tool and API interface design, developer experience, and statistical programming workflows.

**Status:** in development. It is not yet publicly released, and this page will be updated as the project matures.

This is separate from my evaluation of IMPLAN's MCP server, described in [API Enablement, Technical Demos, and MCP Evaluation](api-developer-education.md).

---

## What this work shows

These projects are where my product background and hands-on development meet. The questions are the same ones I ask in product roles: who is this for, what are they trying to do, what will make them trust it, and what will they need in order to adopt it. The difference is that here I also write, test, and ship the software.

For more on how trust in analytical tools is built, see [When Does a Black Box Become Trusted Product Behavior?](https://medium.com/@eclow/when-does-a-black-box-become-trusted-product-behavior-4e4a073cf2a9)

---

## Related Projects

- [TSPDLIB: Open-Source Ecosystem Strategy](tspdlib-library.md)
- [Time-Series Product Development and API Design in GAUSS](time-series-gauss.md)
- [AI Workflow Adoption and Enablement](ai-learning-workflow-enablement.md)
- [API Enablement, Technical Demos, and MCP Evaluation](api-developer-education.md)

---

## Capabilities Demonstrated

- Analytical software development and modernization
- API and developer experience design
- Testing, validation, and reproducibility
- AI-assisted software engineering with human review
- Technical documentation, including documentation for AI assistants
- Packaging, release preparation, and maintenance
- Attribution and open-source stewardship
