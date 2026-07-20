# .ci-negative - DELIBERATE PLANTED FAILURES. DO NOT FIX.

> DO-NOT-FIX. The files in this directory are DELIBERATE. They exist to
> make CI fail in a controlled way. Repairing them defeats the test.

Everything under `.ci-negative/` is a negative E2E case for the hyperi-ci
fleet sweep (branch-mode plan, decision 9). These are NOT bugs in the
fixture and NOT scaffold drift - do NOT "helpfully" fix, format, lint,
patch, or delete them. The boy-scout / fix-as-you-go rule that applies to
the rest of the `ci-test-*` fleet explicitly EXCLUDES this directory.

## How they work

Each `*.patch` is an inert git patch that is NEVER applied on `main` - so
the fixture's own CI stays green. The future sweep, for each case:

1. applies the patch to a fresh throwaway branch `expect-fail/<case>`,
2. pushes + dispatches CI on that branch,
3. asserts CI FAILS at the declared `stage` for the declared `reason`
   (a pass, or a failure at the wrong stage, is itself a red),
4. deletes the branch.

The expected-outcome contract is a header comment at the top of each
patch, in the form:

    # expect: fail / stage: <stage> / reason: <tool>

## Cases

| Patch                  | expect | stage          | reason      |
|------------------------|--------|----------------|-------------|
| `hadolint-error.patch` | fail   | quality        | hadolint    |
| `schema-invalid.patch` | fail   | lint-manifests | kubeconform |

- `hadolint-error.patch` adds `RUN sudo apt-get update` to the Dockerfile.
  hadolint flags DL3004 (do not use sudo) at ERROR severity, which fails
  the container gate inside `hyperi-ci run quality`.
- `schema-invalid.patch` adds a Deployment manifest with `replicas: "three"`
  (a string where the schema requires an integer). kubeconform rejects it,
  failing the `hyperi-ci lint-manifests` gate.
