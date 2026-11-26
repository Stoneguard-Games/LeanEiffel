# Begin with hybrid contract verification

[ADR 0005](0005-require-complete-contract-verification.md) supersedes this decision's original allowance for normal builds with unproved contracts.

LeanEiffel will preserve runtime Design by Contract behavior for preconditions, postconditions, and class invariants while using Lean to prove supported contracts. Programs may run with unproved contracts, and the verification report must clearly identify those contracts. Contracts will be authored in Lean-verifiable syntax as the authoritative specification, replacing Eiffel's existing contract syntax rather than adding a separate proof annotation layer. See [the prototype architecture](0003-companion-lean-contracts-and-stock-gobo.md) for runtime integration and [the validation boundaries](0004-eiffel-and-lean-validation-boundaries.md) for the evidence required to claim a proof.
