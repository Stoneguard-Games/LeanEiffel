# Define validation boundaries for Eiffel and Lean contracts

Stock Gobo is the canonical validator for Eiffel classes and features. Declaring a contract is optional. Lean is the source of truth for the contracts a class declares, and Gobo with assertion monitoring enabled is the reference for runtime behavior, including when checks run, how inherited contracts combine, and how `old` and `Result` are interpreted.

LeanEiffel validates the contract layer at three levels:

| Level | Scope |
| --- | --- |
| A. Lean validity | The contract declarations must parse, elaborate, and type-check in Lean. |
| B. Relationships between classes | Validate the contract relationships that apply when classes interact, including relevant inherited contracts and contracts at call boundaries. |
| C. Relationship to the Eiffel implementation | Validate the contract's binding to the relevant Eiffel class or feature and check that the implementation satisfies its obligations where static verification is supported. |

Report unresolved proof obligations explicitly, including those arising at levels B and C. An unproved obligation can still run with its runtime check. If the frontend cannot generate a runtime assertion for a declared contract, the contract build fails.

Report a routine obligation as proved only for a Lean-checked theorem tied to the corresponding Eiffel implementation, effective contract, and execution model. Disclose the assumptions supporting that claim. Report checker correspondence separately from routine correctness. The initial proof coverage and handling of a demonstrated violation are open design questions.

[Wayfinder decision ticket](https://github.com/Stoneguard-Games/LeanEiffel/issues/5)
