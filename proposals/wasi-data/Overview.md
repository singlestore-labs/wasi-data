# Distributed Data Frame Proposal

## Summary

This proposal extends WASI API with the ability to distribute computation of data streams.

## Motivation


Define a portable, modular, runtime-independent, and WebAssembly-native API
   to serve as a system interface which can be used by WebAssembly code to
   interact with the outside world, that preserves the essential sandboxed
   nature of WebAssembly through a [Capability-based] API design.

Secure, portable environment.

On the edge data transformations
