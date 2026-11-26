# Require full routine and inherited contract guarantees

Within the verified scope, prove that every class and feature meets its applicable preconditions, postconditions, and class invariants. At a call, prove that the caller establishes the callee's effective precondition. Prove that a routine fulfills its guarantees for every admitted input and state, including the postcondition on return and the invariant at the applicable Eiffel boundaries. Creation must establish the class invariant. Verify termination when completion is promised, and rule out undeclared exceptions. An intended long-running routine must declare that behavior explicitly.

For an inherited feature, compare the effective contracts contributed by each applicable ancestor after resolving Eiffel inheritance to the descendant feature. A descendant must accept the union of the ancestor input domains. Each ancestor precondition implies the descendant's effective precondition. The descendant may accept additional inputs.

Every normal return of the descendant must satisfy the intersection of all inherited postconditions, together with its own applicable postconditions. This applies to all admitted inputs, including an input accepted by only one ancestor. Apply all effective class invariants at their required boundaries. A required obligation without a proof fails the build, including an inherited postcondition combination that cannot be satisfied for an admitted input.

Use stock Gobo with assertion monitoring enabled as the runtime reference for contract boundaries and test the generated assertions against the accepted Lean contracts. Existing Eiffel dependencies can enter through [declared interfaces](0006-allow-incremental-eiffel-adoption.md). Their disclosed assumptions and warnings remain part of the result.

[Wayfinder semantics ticket](https://github.com/Stoneguard-Games/LeanEiffel/issues/5)
