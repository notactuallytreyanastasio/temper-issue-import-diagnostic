# UX: Explicit intra-library import produces opaque `Expected a TopLevel here`

Temper `fbcc92f` — confirmed 2026-05-30

## Summary

Temper libraries use a single shared namespace; modules do not import
each other. When a developer writes an explicit import (natural first
instinct coming from TypeScript, Rust, Python, etc.), the error is:

`@P: Expected a TopLevel here`

This gives no indication of why the import failed, that imports between
modules in the same library are unnecessary, how the shared namespace
works, or what to do instead.

## Repro — the import below triggers the diagnostic

    import { anything } from "./some-module";

    test("placeholder") {
      assert(true) { "check the error on the import above" };
    }

## Actual error output

Running `temper build` on this file produces:

`@P: Expected a TopLevel here` pointing at the `import` keyword.

## Expected error output

Something actionable, e.g.:

`error: Explicit imports are not needed in Temper. All modules in a library share a namespace — 'anything' is available directly by name if it is exported from another module in this library.`

## Why this matters

The shared-namespace design is a deliberate Temper feature, but it is
surprising to anyone coming from a language with explicit module systems.
This parse error is a high-traffic first encounter for new users. A clear
diagnostic turns a confusing dead-end into a 30-second explanation.

## Context

Found porting the chess9 TypeScript engine to Temper. The original TypeScript
used explicit imports between every module. Every file attempted an import
before discovering the shared namespace. See:
https://github.com/notactuallytreyanastasio/chess9
