# Begin with hybrid contract verification

LeanEiffel will retain runtime checks for preconditions, postconditions, and class invariants while using Lean to prove supported contracts. Programs may run with unproved contracts, and the verification report must clearly identify those contracts. This starting model allows incremental proof coverage without requiring every contract to be proved before a program can run; the supported contract subset and the relationship between Lean proofs and executable Eiffel semantics remain open design decisions.
