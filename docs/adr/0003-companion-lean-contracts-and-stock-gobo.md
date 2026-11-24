# Use companion Lean contracts and stock Gobo for the prototype

Authoritative preconditions, postconditions, and class invariants live in companion `.lean` files that explicitly bind to Eiffel classes and features. The LeanEiffel frontend will generate Eiffel assertions from supported executable Lean predicates, retain source locations for diagnostics, and pass the generated code to stock Gobo for compilation and runtime enforcement.

Companion files let contract authors use normal Lean modules and tooling. Generated assertions let the prototype reuse Gobo's assertion machinery. The prototype must test whether the generated assertions agree with the Lean predicates on the program state that Gobo exposes. Exact binding syntax, supported predicates, and the guarantees of a proof remain open.

[Wayfinder decision](https://github.com/Stoneguard-Games/LeanEiffel/issues/4)
