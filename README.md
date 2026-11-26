# LeanEiffel

An Eiffel-derived language and integrated toolchain for AI coding agents, built on the existing [Gobo Eiffel compiler](https://github.com/gobo-eiffel/gobo) and [Lean 4](https://github.com/leanprover/lean4).

LeanEiffel is being designed to replace Eiffel's existing Design by Contract syntax with Lean-verifiable contracts for preconditions, postconditions, and class invariants. The normal build succeeds only when Eiffel compilation, Lean validation, and all required contract proofs succeed. Generated assertions enforce the contracts at runtime. Failed builds provide source locations and explanations for humans and coding agents.

The project is currently in planning. Start with the [agreed intent](docs/wayfinder/intent.md), [glossary](GLOSSARY.md), and [Wayfinder maps](https://github.com/Stoneguard-Games/LeanEiffel/issues?q=is%3Aissue%20label%3Awayfinder%3Amap). The first milestone is an implementation-ready specification, with a command-line interface for both agents and humans.

AI Hero's skills are installed in `.agents/skills/`; their source revisions are recorded in `skills-lock.json`.
