# LeanEiffel

LeanEiffel is an Eiffel-derived programming language and integrated toolchain intended for use by AI coding agents, built on the existing Gobo Eiffel compiler and Lean 4.

## Language

**Lean contract**:
The authoritative specification of a LeanEiffel precondition, postcondition, or class invariant, written in a companion `.lean` module linked to its Eiffel class or feature.

**Hybrid verification**:
LeanEiffel's combination of runtime contract enforcement and Lean proofs.

**Verified build**:
A LeanEiffel build that passes Eiffel compilation, Lean validation, and proof of every required contract obligation.
