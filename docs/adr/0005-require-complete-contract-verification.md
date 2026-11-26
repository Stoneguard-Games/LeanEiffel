# Require complete contract verification for a successful build

LeanEiffel provides one binary verdict for the normal build. Return success only when stock Gobo compiles the Eiffel program, Lean accepts the contract declarations, validation succeeds across interacting classes and against the Eiffel implementations, and all required precondition, postcondition, and class invariant obligations are covered and proved. A compilation error, invalid declaration, failed validation, demonstrated violation, or required obligation that is missing, unsupported, or unproved returns failure. A required runtime assertion must also be generated successfully.

Success certifies the build against its formal contracts and execution model, including any disclosed assumptions. The specification must define which program behavior the required coverage includes. [Declared interfaces for existing Eiffel classes](0006-allow-incremental-eiffel-adoption.md) identify dependency assumptions that can qualify success. Runtime assertion enforcement remains part of Design by Contract.

The verdict is the signal an agent can use to accept or revise a build. Failures should identify the original Eiffel or Lean source location as precisely as the tools allow and explain the problem. When an error comes from generated assertions, map it back to the originating contract and related Eiffel feature. The CLI will provide machine-readable diagnostics and readable explanations.

This decision supersedes the earlier policy allowing normal builds with unproved contracts.

[Wayfinder semantics ticket](https://github.com/Stoneguard-Games/LeanEiffel/issues/5)
