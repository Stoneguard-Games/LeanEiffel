# Allow incremental adoption of existing Eiffel classes

A verified LeanEiffel build may call or inherit from an existing Eiffel class without Lean validation through an explicitly declared and checked interface. The interface defines what verified code may assume about the dependency. The build still checks the obligations on the verified side of the boundary, including those introduced by calls or inheritance. Success is conditional on the declared dependency assumptions.

Warn for each called or inherited class whose use lacks Lean validation. Identify the class, whether it is called or inherited, the source location when available, and whether the class has native Eiffel Design by Contract or has no contracts. These warnings apply even when the build succeeds. Gobo can enforce native Eiffel assertions with assertion monitoring enabled. The verification report records dependency assumptions alongside the warnings.

A dependency warning does not discharge a required proof obligation within verified code. Missing or unproved obligations in that scope fail the build.

[Wayfinder semantics ticket](https://github.com/Stoneguard-Games/LeanEiffel/issues/5)
