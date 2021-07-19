# Distributed Data Frame Proposal

## Summary

This proposal is for a new WASI API that would allow distributed systems to create computational DAG's with WASM modules using map, filter, and join semantics.

## Motivation

Define a portable, modular, runtime-independent, and WebAssembly-native API
   to serve as a system interface which can be used by WebAssembly code to
   interact with the outside world, that preserves the essential sandboxed
   nature of WebAssembly through a [Capability-based] API design.
