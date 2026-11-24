# Gobo contract integration: source reconnaissance

Research ticket: [Investigate Gobo integration for replacing Eiffel contract syntax](https://github.com/Stoneguard-Games/LeanEiffel/issues/2). Planning map: [Chart LeanEiffel's Gobo and Lean contract integration](https://github.com/Stoneguard-Games/LeanEiffel/issues/1).

Inspected 2026-11-24: Gobo master resolved to **`55e703f38f1f167520e74a9c01af7a3f65a23c23`**, committed 2026-11-14. All source citations below are pinned to that revision. Method: GitHub API tree and raw source reads, no clone, build, execution, repository edits, or GitHub mutation. This is focused reconnaissance, not an exhaustive audit.

## Scope and finding

LeanEiffel is to reuse the **existing Gobo compiler** and Lean 4. Its authoritative contract language replaces author-written Eiffel `require`/`ensure`/`invariant` contracts; generated Eiffel contracts could be a compiler intermediate artifact, not a second author-maintained specification. Runtime DbC remains, and unproved contracts are reported explicitly while remaining runnable. Embedded versus companion contract placement remains undecided.

**Observed:** Gobo exposes useful Eiffel-library integration seams and already implements the contract runtime machinery. **Inference:** both external lowering to ordinary Eiffel and an extension of Gobo's frontend are credible experiments. Neither has been demonstrated here; this note selects neither architecture nor contract placement.

## Concrete source entry points

| Area | Observed source behavior |
| --- | --- |
| Existing compiler | `gec` is mainly based on Gobo Eiffel Tools and normally consumes an ECF system. Its repository includes bootstrap C and runtime sources. [Compiler README][readme] |
| Parsing and AST | `ET_EIFFEL_PARSER_SKELETON.parse_file` and `parse_class_text` accept streams or UTF-8 text with filename, timestamp and group context. `ET_SYSTEM_PROCESSOR` exposes an `ET_AST_FACTORY`, preparser, parser, ancestor builder, feature flattener, interface checker and implementation checker, with setters for the factory/parser. These are Eiffel class interfaces; they do not establish a stable external plugin ABI. [Parser input][parse] · [Processor seams][seams] |
| Contract grammar | The grammar has explicit `E_REQUIRE`/`E_ELSE`, `E_ENSURE`/`E_THEN`, and `E_INVARIANT` productions. Assertion bodies are Eiffel expressions (plus tagged and special class assertions). Parser actions construct precondition/postcondition/invariant nodes through factory helpers. Adding a differently parsed contract language is beyond merely substituting an AST factory. [Grammar][grammar] |
| AST provenance | Pre/postcondition nodes retain `implementation_closure`, keyword/source-position information, and `is_require_else`/`is_ensure_then`; both dispatch through `ET_AST_PROCESSOR`. This is useful for tracing inherited contracts back to the declaring closure. [Preconditions][preast] · [Postconditions][postast] |

The current parser also has fast parsing followed by diagnostic reparsing with a decorated AST factory. Any frontend extension must account for both paths; changing only one parser path may produce inconsistent acceptance or diagnostics. This is a maintenance implication, not an experimentally established failure. [Parser fallback][fallback]

## Runtime and inheritance semantics that an integration must preserve

- **Runtime checking is substantive generated behavior.** `ET_C_GENERATOR.print_assertions` evaluates each assertion expression and emits a failure call to `GE_raise_with_message` with exception code and assertion tag. Entry generation gates invariants/preconditions/old-value capture on compiler settings; footer generation checks invariants and postconditions. `False` postconditions have an explicit exception even when ordinary postcondition monitoring is disabled. Proof status alone is not a switch in this inspected code. [Assertion emission][assertemit] · [Entry scheduling][entry] · [Exit/False behavior][exit]
- **Effective contracts include inheritance.** The dynamic builder collects precursor pre/postconditions, records index offsets, and collects ancestor invariants. A root precursor with absent/true preconditions makes the effective precondition implicitly true. The generator tries successive precondition groups, failing only on the last group, while postcondition generation checks every collected group. This is the source basis for the ordinary disjunctive inherited precondition / conjunctive inherited postcondition model. Multiple inheritance, renaming, redefinition and generic adaptation therefore cannot be modeled by reading only the local clause. [Collection][collection] · [Precondition alternatives][preemit] · [Postcondition accumulation][postemit]
- **Redeclaration markers matter.** The flattener reports nonfatal validity errors when a redeclared precondition lacks `require else`, or a postcondition lacks `ensure then`. A generated Eiffel representation must supply the appropriate markers. `flat_dbc_mode` means rechecking inherited contracts in redeclarations during compiler validity checking; it is not Lean proof checking. [Redeclaration validation][redeclaration] · [Flat DbC meaning][flatdbc]
- **`old` captures expression values before the body, with exception behavior.** The checker permits old expressions only in postconditions. Code generation creates a typed temporary and exception marker, evaluates the operand before the body, catches evaluation exceptions, and re-raises the stored old exception when its value is used. The inspected assignment is value capture, not evidence of an automatic deep heap snapshot. Lean heap/pre-state semantics must distinguish `old object_reference` from `old object_reference.field`. [Old validity][oldvalid] · [Capture][oldcapture] · [Exception capture wrapper][oldwrap] · [Use/re-raise][olduse]
- **`Result` has language context and type.** The checker rejects it in preconditions/invariants, requires a function result type, and explicitly resolves an inherited assertion's Result against the descendant feature signature. Generation contains additional expanded/once/rescue handling. Treating it as an untyped identifier or as the ancestor's fixed return type would lose source semantics. [Result validity][resultvalid] · [Result generation][resultgen]
- **Invariant and concurrency boundaries need care.** Generated invariant calls distinguish qualified-call entry/exit and inherited invariants. Precondition generation explicitly handles SCOOP waiting preconditions. A sequential proof subset cannot silently claim those concurrency semantics are covered. [Invariant call guard][invguard] · [SCOOP-aware preconditions][preemit]

## Practical integration paths to investigate

| Candidate experiment | What existing source supports | What remains untested |
| --- | --- | --- |
| External contract frontend producing Eiffel plus Lean obligations | Keep stock `gec`, ECF, type checking and runtime generation; emit ordinary Eiffel assertion nodes/text from the one authoritative contract representation. Parser text input also offers a possible in-process frontend seam. | Correct lowering of state, types, inheritance, `old`, evaluation order and diagnostics; source maps; detecting stale Lean evidence; ensuring users never maintain two independent contracts. |
| Gobo frontend/library extension | Replace/wrap parser and factory/processors, lower contracts into existing typed assertion forms, and reuse inheritance and C runtime generation. | Required changes to scanner, generated grammar/parser paths and compiler driver; build/bootstrapping cost; upstream compatibility; whether a precise semantic/provenance representation survives all phases. |

These are integration sketches, not approved implementation plans. Embedded versus companion files is orthogonal: either placement still needs name binding, authoritative-source ownership, runtime lowering and proof/runtime correspondence.

**Existing preprocessing is limited:** `gepp` is documented as a small C-like portability filter; its grammar supports conditional directives, define/undefine and include. It is not an observed Lean parser or typed contract translator. No ready-made custom-contract-language plugin or Lean bridge was found in the inspected entry points; this is not proof none exists elsewhere. [Preprocessor README][gepp] · [Directive grammar][geppgrammar]

**Upstream limit:** compiler documentation says feature replication under nonconforming inheritance still uses pre-ECMA `select` semantics, and its limitations list is explicitly nonexhaustive. Compatibility claims must be pinned to Gobo behavior, not an assumed complete Eiffel standard implementation. [Compiler limitations][limits]

## Minimal evidence-producing experiments before an architecture decision

1. Compile a small unmodified Gobo baseline with assertions enabled, then generate equivalent Eiffel from a minimal authoritative contract input. Compare pass/fail tags and exception behavior, not just compilation.
2. Exercise redefinition, missing/true ancestor preconditions, multiple inheritance/renaming, strengthened postconditions, inherited generic/covariant Result, qualified versus unqualified calls, and creation routines.
3. Compare `old reference` versus `old field`, aliased heap mutation, short-circuit postconditions and failing old operands. Test assertion monitoring settings and `False` postconditions separately.
4. Build a tiny Eiffel Tools client using `parse_class_text`, factory/processor substitution and inherited typed assertions; test valid input and syntax-error reparsing. Measure the actual frontend patch/build surface against external lowering.
5. For one small semantic subset, derive executable checks and Lean obligations from the same contract representation. Demonstrate proved, failed, unsupported and timed-out proof outcomes with explicit reporting while retaining runtime checks. Separately justify the connection between executable Gobo program semantics and the proved proposition; a proved contract formula alone does not prove the routine implementation.

No Lean integration, executable semantics model, trusted lowering boundary, compiler build or semantic equivalence experiment was validated in this investigation. Arbitrary Lean propositions may lack an executable decidable check; the supported contract language/runtime interpretation must be established before promising universal runtime coverage.

[readme]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/tool/gec/Readme.md#L1-L36
[parse]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/parser/et_eiffel_parser_skeleton.e#L122-L183
[seams]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/compilation/et_system_processor.e#L792-L875
[grammar]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/parser/et_stop_on_first_error_eiffel_parser.y#L2514-L2613
[preast]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/ast/assertion/et_preconditions.e#L55-L181
[postast]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/ast/assertion/et_postconditions.e#L55-L181
[fallback]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/parser/et_eiffel_parser.e#L25-L90
[assertemit]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/generation/et_c_generator.e#L40958-L41004
[entry]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/generation/et_c_generator.e#L2888-L2912
[exit]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/generation/et_c_generator.e#L3194-L3208
[collection]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/dynamic/et_dynamic_type_builder.e#L927-L1032
[preemit]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/generation/et_c_generator.e#L40472-L40686
[postemit]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/generation/et_c_generator.e#L40703-L40743
[redeclaration]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/compilation/et_feature_flattener.e#L915-L929
[flatdbc]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/compilation/et_system_processor.e#L993-L998
[oldvalid]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/compilation/et_feature_checker.e#L9897-L9939
[oldcapture]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/generation/et_c_generator.e#L40819-L40868
[oldwrap]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/generation/et_c_generator.e#L41135-L41257
[olduse]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/generation/et_c_generator.e#L15325-L15369
[resultvalid]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/compilation/et_feature_checker.e#L10841-L10930
[resultgen]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/generation/et_c_generator.e#L15975-L16030
[invguard]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/library/tools/src/eiffel/generation/et_c_generator.e#L40434-L40470
[gepp]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/tool/gepp/Readme.md#L1-L15
[geppgrammar]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/tool/gepp/src/gepp_parser.y#L40-L104
[limits]: https://github.com/gobo-eiffel/gobo/blob/55e703f38f1f167520e74a9c01af7a3f65a23c23/tool/gec/doc/limitations.xml#L28-L65
