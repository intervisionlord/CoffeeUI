# THE MANIFESTO OF LOCAL AI INFERENCE

### A Technical Architecture Proposal

---

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

                    T H E   M A N I F E S T O
                              of
                  L O C A L   A I   I N F E R E N C E

              An Architecture for the Next Generation
                 of Local Machine Learning Tooling

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

                         Version 1.0
                         Draft Specification

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## ABSTRACT

This document specifies a new architecture for local AI inference tooling. It is written in response to the systemic failures of existing monolithic frameworks, which have demonstrated that the "one process, one pot" model cannot scale to meet the demands of modern multimodal pipelines.

The architecture proposed here is not an incremental improvement. It is a **redefinition of the fundamental unit of composition**: from "a function inside a monolith" to "a service with a contract." Every subsequent principle follows from this single change.

This document contains no code, no configuration examples, and no implementation details. It is a **declaration of principles and architecture** — a foundation upon which implementations may be built.

---

## TABLE OF CONTENTS

```
I.    PREAMBLE
II.   THE PROBLEM
III.  CORE PRINCIPLE
IV.   THE FACADE
V.    THE FILE SYSTEM AS TRUTH
VI.   MANIFEST AS CONTRACT
VII.  ENVIRONMENT ISOLATION
VIII. MEMORY AS A FIRST-CLASS CONCERN
IX.   ROUTING
X.    COMPOSITION
XI.   PERFORMANCE
XII.  RELIABILITY
XIII. LOCAL-FIRST, CLOUD-OPTIONAL
XIV.  WHAT THIS GIVES
XV.   THE MANIFESTO
XVI.  THE CALL
```

---

## I. PREAMBLE

We are tired.

Tired of software that breaks what used to work. Tired of "optimizations" that make things worse. Tired of updates that destroy compatibility. Tired of being told the problem is on our side. Tired of watching a tool we loved become a vehicle for someone else's business model.

This is not a bug report. This is not a feature request. This is a **statement of how things should be built**.

---

## II. THE PROBLEM

A certain class of local AI inference frameworks — the kind that grew from a weekend project into a venture-funded product — suffers from architectural defects that are not fixable by patching. They are inherent to the monolith:

- **Shared memory pool.** All models live in one process. A leak in one kills the session for all.
- **Shared environment.** All nodes share one set of dependencies. Conflicts are inevitable.
- **Shared lifecycle.** All components live and die together. Isolation of failure is impossible.
- **Hidden state.** State lives in a database, not on disk. It desynchronizes, corrupts, and obstructs.
- **Fragile updates.** Updating the framework breaks compatibility with existing models and workflows.
- **Opaque memory management.** "Smart" mechanisms produce regressions instead of optimizations, and their authors close issues with dismissive one-liners.
- **Slow I/O.** Standard-library serialization. Synchronous HTTP. No efficient binary transfer.

These defects are not bugs. They are **consequences of the architecture**. They cannot be fixed by patches because they are the patches.

---

## III. CORE PRINCIPLE

**A node is not a function. A node is a service.**

In the current paradigm, a node is a piece of code inside the monolith. It executes in the shared process. It consumes shared memory. It depends on the shared environment.

In the proposed paradigm, a node is a **description of a call to an external application**. It contains no logic. It contains a contract: what to send, where to send it, what to receive.

Each model is a **separate process**. Each process is a **separate application**. Each application is a **self-contained unit** with its own lifecycle, its own dependencies, its own memory.

From this single principle, everything else follows.

---

## IV. THE FACADE

Between the user and the applications stands the **facade**.

The facade is the single point of entry. It knows what is installed. It knows how to start, stop, and restart. It routes requests. It manages memory. It queues tasks. It monitors health.

The facade is not a monolith. It is an **orchestrator**. It does not execute models. It does not contain inference logic. It **coordinates**.

The UI talks only to the facade. Applications talk only to the facade. There are no direct connections between the UI and applications. This is not a limitation. This is the design.

---

## V. THE FILE SYSTEM AS TRUTH

The state of the system is the state of the file system.

There is no database. There is no hidden state. There is nothing to desynchronize.

- An application is **installed** if there is a folder with a valid manifest.
- An application is **ready** if there is an environment.
- An application is **running** if there is a process.
- An application is **unloaded** if there is no process.

This is not a simplification. This is a **guarantee**. What the user sees on disk is what the system knows.

---

## VI. MANIFEST AS CONTRACT

Every application declares itself through a **manifest**.

The manifest describes what the application is, what it needs, and what it offers. It is not documentation. It is not a comment. It is a **contract**.

From the manifest, the facade learns:
- How to isolate the application.
- What dependencies it requires.
- What inputs it accepts and what outputs it produces.
- How much memory it needs.
- How to interact with it.

The manifest is the single source of truth for everything about the application. Nothing is implicit. Nothing is assumed.

---

## VII. ENVIRONMENT ISOLATION

Dependency conflicts are eliminated by design.

Every application receives the environment it needs. If its dependencies are compatible with the base set, it shares the base environment. If its dependencies conflict, it receives an isolated environment. If it requires a different language version or system dependencies, it is containerized.

The user does not think about this. The user installs the application. The facade decides how to isolate it.

This is not a feature. This is the **absence of a bug**.

---

## VIII. MEMORY AS A FIRST-CLASS CONCERN

Memory is not managed by "smart" mechanisms. Memory is managed by the user.

Every application declares whether it is **persistent** (always in memory) or **on-demand** (loaded on request, unloaded after use). Every application declares its **budget**. The facade enforces it.

The key principle: **memory is released by death, not by hope**. We do not "unload models" and pray. We terminate processes and know. The operating system guarantees what a garbage collector cannot.

This is not an optimization. This is **correctness**.

---

## IX. ROUTING

Conditional transitions are not a hack. They are a primitive.

A router is a node. It looks at its input — a flag, a metadata field, a content signature — and decides where to send it. It does not duplicate graphs. It does not require third-party nodes. It does not require workarounds.

Routing is a first-class feature. It belongs in the foundation, not in the ecosystem.

---

## X. COMPOSITION

Any graph can be compiled into an application. Any application can be used as a node in another graph.

This recursion is not a bug. It is the point.

Pipelines can be built from pipelines. Applications can be composed from applications. Each layer is isolated, testable, versionable, and shareable. There is no limit to the depth. There is no boundary to the composition.

This is not subgraphs. These are **real applications**, compiled from graphs, usable as nodes, and sharable as units.

---

## XI. PERFORMANCE

Performance is a foundation, not an afterthought.

Fast serialization. Asynchronous I/O. Shared memory for tensor transfer between local applications. Task queues with concurrency control.

Speed is not a luxury. It is **respect for the user's time**. A framework that is slower than its predecessor is not an upgrade. It is a regression.

---

## XII. RELIABILITY

Reliability is not a feature. It is the absence of failure.

Applications report their health. They shut down gracefully. They restart automatically. They log. They are monitored. Their failures are isolated. Their deaths are clean.

This is not an aspiration. This is the **minimum standard**.

---

## XIII. LOCAL-FIRST, CLOUD-OPTIONAL

The same contract works for a process on `localhost` and an endpoint in the cloud. The distinction is a transport detail, not an architectural one.

Users who want to run everything on their own hardware — do it. Users who want to offload heavy models to a paid API — do it. The same graph works for both. The user decides where compute lives. The architecture does not force either.

This is not a compromise. This is **the correct design**.

---

## XIV. WHAT THIS GIVES

**To the user:**
- Stability. Updating one model does not break the others.
- Control. Explicit memory management. No surprises.
- Transparency. Everything is visible on the file system.
- Speed. Fast I/O. Isolation. Parallelism.
- Flexibility. Routing. Composition. Sharing.
- Extensibility. Any application by contract.

**To the developer:**
- Independence. Each application evolves separately.
- Simplicity. No compatibility with the monolith to maintain.
- Testability. Each application is tested in isolation.
- Reusability. Wrote once. Used everywhere.

---

## XV. THE MANIFESTO

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  We believe that isolation is better than integration.

  We believe that explicit control is better than automatic magic.

  We believe that transparency is better than hidden state.

  We believe that stability is better than velocity.

  We believe that composition is better than monoliths.

  We believe that contracts are better than assumptions.

  We believe that processes are better than threads.

  We believe that manifests are better than databases.

  We believe that the user is better than the metric.

  We believe that software should work.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## XVI. THE CALL

Build this.

Not as a fork. Not as a patch. As a **new system** built on these principles.

If you cannot build it — do not obstruct those who can.

If you will not build it — do not mock those who try.

If you do not understand it — do not dismiss it as "dumb."

Because the people who complain are not the problem.

**The people who ignore them are.**

---

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

                    END OF DOCUMENT

           This manifesto is open. Fork it. Extend it.
              Contradict it. Build something better.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
