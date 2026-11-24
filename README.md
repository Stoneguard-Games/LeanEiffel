# LeanEiffel

An Eiffel-derived language and integrated toolchain for AI coding agents, built on the existing [Gobo Eiffel compiler](https://github.com/gobo-eiffel/gobo) and [Lean 4](https://github.com/leanprover/lean4).

LeanEiffel is being designed to replace Eiffel's existing Design by Contract syntax with Lean-verifiable contracts for preconditions, postconditions, and class invariants. These Lean contracts are the source of truth, with both runtime enforcement and proofs where supported. Contracts that remain unproved must be clearly reported.

The project is currently in planning. Start with the [agreed intent](docs/wayfinder/intent.md), [glossary](GLOSSARY.md), and [Wayfinder maps](https://github.com/Stoneguard-Games/LeanEiffel/issues?q=is%3Aissue%20label%3Awayfinder%3Amap). The first milestone is an implementation-ready specification, with a command-line interface for both agents and humans.

AI Hero's skills are installed in `.agents/skills/`; their source revisions are recorded in `skills-lock.json`.
