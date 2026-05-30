# temper-issue-import-diagnostic

Repro for a confusing diagnostic in Temper `fbcc92f`.

## The issue

Writing a JS-style import gives a parse error that doesn't explain the correct syntax:

```
import { anything } from "./some-module";
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
@P: Expected a TopLevel here
```

## The correct syntax

Per Tom from the Temper team, the correct form for importing from another module is:

```temper
let { anything } = import("./some-module");
```

Note: `"./some-module"` is expected to be a subdirectory — Temper modules are directories.

## Why this repro exists

The diagnostic `Expected a TopLevel here` gives no hint of the above. A developer
coming from TypeScript, Rust, or Python will assume the path or syntax is wrong
and try variations rather than discovering the correct `let { x } = import(...)` form.

A targeted diagnostic (e.g. "did you mean `let { anything } = import(\"./some-module\")`?")
would turn this into a one-line fix. That improvement is tracked at
https://github.com/temperlang/temper/issues/429

## How to reproduce

```
git clone https://github.com/notactuallytreyanastasio/temper-issue-import-diagnostic
cd temper-issue-import-diagnostic
temper build
# @P: Expected a TopLevel here  (on the import line)
```

## Version

Temper `fbcc92f`. Reproduced 2026-05-30.
