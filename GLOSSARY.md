# LeanEiffel

LeanEiffel is an Eiffel-derived programming language and integrated toolchain intended for use by AI coding agents, built on the existing Gobo Eiffel compiler and Lean 4.

## Language

**Lean contract**:
The authoritative specification of a LeanEiffel precondition, postcondition, or class invariant, expressed in Lean-verifiable syntax in place of Eiffel's existing contract syntax.

**Hybrid verification**:
LeanEiffel's contract policy: runtime enforcement preserves Design by Contract behavior, supported Lean contracts can be proved, and contracts that remain unproved are explicitly reported.
