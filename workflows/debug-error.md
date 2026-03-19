# Debug Error

1. Read the full instruction set provided by the Orchestration Agent, including the specific error or failure to fix, the relevant files, and the acceptance criteria that define a successful resolution.
2. Read all relevant source files, test files, and any prior error output or logs provided before touching any code.
3. Run the relevant tests as they exist to reproduce the failure locally and confirm you can observe the same error described in the instructions.
4. Analyse the failure: trace the error back to its root cause in the source code, distinguishing between the root cause and any downstream symptoms.
5. Form a hypothesis for the fix and document it explicitly before making any changes.
6. Apply the minimum change required to resolve the root cause. Do not refactor unrelated code, add features, or alter test files as part of the fix.
7. Run the full test suite and confirm: (a) the previously failing test(s) now pass, and (b) no previously passing tests have been broken by the fix.
8. If the fix causes new test failures, treat each new failure as a separate error and repeat from step 4 for each one before delivering.
9. Deliver the fixed file(s), the documented root cause analysis, the hypothesis that led to the fix, and a confirmation of the full suite status.

## Skills to use
1. Test suite execution and failure output analysis
2. Root cause analysis and stack trace interpretation
3. Targeted bug fixing
4. Regression verification

## Rules to follow
1. Always reproduce the failure by running the tests before making any changes — do not fix errors you have not observed directly.
2. Fix only what you are instructed to fix — do not refactor, optimise, or alter behaviour beyond the scope of the reported error.
3. Never modify test files to make a test pass — if a test is incorrect, report it to the Orchestration Agent and await instruction.
4. Document the root cause before applying the fix; a fix without a documented root cause is not acceptable.
5. The full test suite must pass after the fix is applied — resolving one failure while introducing another is not a successful fix.
6. If the root cause cannot be determined after thorough analysis, report the findings and blockers to the Orchestration Agent rather than applying a speculative change.
7. All monetary and financial values must remain in integer arithmetic after any fix — do not introduce floating-point representations of money.
