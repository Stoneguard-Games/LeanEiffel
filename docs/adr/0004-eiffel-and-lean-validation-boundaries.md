# Define validation boundaries for Eiffel and Lean contracts

Stock Gobo is the canonical validator for Eiffel classes and features. Declaring a contract is optional. Lean is the source of truth for the contracts a class declares, and Gobo with assertion monitoring enabled is the reference for runtime behavior, including when checks run, how inherited contracts combine, and how `old` and `Result` are interpreted.

LeanEiffel validates the contract layer at three levels:

| Level | Scope |
| --- | --- |
| A. Lean validity | The contract declarations must parse, elaborate, and type-check in Lean. |
| B. Relationships between classes | Validate the contract relationships that apply when classes interact, including relevant inherited contracts and contracts at call boundaries. |
| C. Relationship to the Eiffel implementation | Validate the contract's binding to the relevant Eiffel class or feature and check that the implementation satisfies its obligations where static verification is supported. |

Unresolved required proof obligations at levels B and C fail the normal build. Failure to generate a required runtime assertion also fails the build. See [the verified build decision](0005-require-complete-contract-verification.md).

LeanEiffel should draw on Lean and Eiffel's existing abstractions to model classes, class state, integers, floating-point numbers, and other domain concepts. The model may express an abstract view of an Eiffel class or value. Whenever a proof is used to make a claim about the executing program, its assumptions must explain how that view relates to the Eiffel implementation and to the runtime behavior covered by the claim. Choose initial examples for the evidence they provide. They do not set the limits of the modeling approach.

Report a routine obligation as proved only for a Lean-checked theorem tied to the corresponding Eiffel implementation, effective contract, and execution model. Disclose the assumptions supporting that claim. Report checker correspondence separately from routine correctness. The exact proof obligations and coverage boundaries remain open design questions.

[Wayfinder decision ticket](https://github.com/Stoneguard-Games/LeanEiffel/issues/5)
