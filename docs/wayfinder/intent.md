# LeanEiffel: agreed intent

The first Wayfinder effort will produce an implementation-ready specification for the first working LeanEiffel version, supported by feasibility research into the Gobo and Lean integration.

## Accepted direction

- Build an Eiffel-derived language and integrated toolchain for use by AI coding agents.
- Use the existing Gobo Eiffel compiler and Lean 4 as the project foundations. See [the compiler foundation decision](../adr/0002-build-on-existing-gobo-and-lean.md).
- Retain runtime checks for preconditions, postconditions, and class invariants, use Lean to prove supported contracts, and clearly report contracts that remain unproved. See [the hybrid verification decision](../adr/0001-hybrid-contract-verification.md).
- Provide a command-line workflow with machine-readable diagnostics alongside readable output for humans. Diagnostics should identify each contract, its source location, and its verification result.

## Open design work

Integration architecture, the meaning and scope of proof claims, proof authoring, exact diagnostic formats, platform support, and first-release acceptance examples remain to be decided through the Wayfinder map. No architecture has been selected and no executable prototype has been built.

## Sources

- [Gobo Eiffel upstream](https://github.com/gobo-eiffel/gobo). No Gobo fork was visible under Stoneguard-Games when this effort began; the intended upstream or fork remains to be confirmed if a different repository is required.
- [Lean 4 upstream](https://github.com/leanprover/lean4).
