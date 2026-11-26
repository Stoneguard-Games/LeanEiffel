# LeanEiffel: agreed intent

The first Wayfinder effort will produce an implementation-ready specification for the first working LeanEiffel version, supported by feasibility research into the Gobo and Lean integration.

## Accepted direction

- Build an Eiffel-derived language and integrated toolchain for use by AI coding agents.
- Use the existing Gobo Eiffel compiler and Lean 4 as the project foundations. See [the compiler foundation decision](../adr/0002-build-on-existing-gobo-and-lean.md).
- Replace Eiffel's existing Design by Contract syntax with Lean-verifiable contract syntax. Lean contracts are the authoritative specification of preconditions, postconditions, and class invariants; they are not an additional annotation layer over separately authored Eiffel contracts.
- Preserve runtime Design by Contract behavior, use Lean to prove supported contracts, and clearly report contracts that remain unproved. See [the hybrid verification decision](../adr/0001-hybrid-contract-verification.md).
- Provide a command-line workflow with machine-readable diagnostics alongside readable output for humans. Diagnostics should identify each contract, its source location, and its verification result.
- Keep authoritative contracts in companion `.lean` files. For the prototype, generate Eiffel assertions and compile them with stock Gobo. See [the contract placement and compiler integration decision](../adr/0003-companion-lean-contracts-and-stock-gobo.md).
- Keep Gobo as the canonical Eiffel validator and make contract declarations optional. Validate Lean contracts as Lean declarations, against relevant contracts of interacting classes, and against the Eiffel features they govern. See [the validation boundaries decision](../adr/0004-eiffel-and-lean-validation-boundaries.md).
- Use Lean and Eiffel's existing expressive power to model classes, integers, floating-point numbers, and other domain concepts. Relate those models to Eiffel execution when claiming a proof about the program. See [the validation boundaries decision](../adr/0004-eiffel-and-lean-validation-boundaries.md).

## Open design work

Contract binding syntax, supported contract expressions, initial proof coverage, policy for demonstrated violations, proof authoring, exact diagnostic formats, platform support, and first-release acceptance examples remain open. The prototype will test whether generated Eiffel assertions agree with the authoritative Lean predicates.

## Sources

- [Gobo Eiffel upstream](https://github.com/gobo-eiffel/gobo). No Gobo fork was visible under Stoneguard-Games when this effort began; the intended upstream or fork remains to be confirmed if a different repository is required.
- [Lean 4 upstream](https://github.com/leanprover/lean4).
