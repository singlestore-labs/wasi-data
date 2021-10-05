# Summary

The goal of this proposal is to define a mechanism for programs compiled to Wasm to represent and drive distributed algorithms, taking advantage of distributed storage and computation systems.

Apache Spark is one such example. The main abstraction Spark provides is a resilient distributed dataset (RDD), which is a fault-tolerant collection of elements that can be operated on in parallel.

The attributes of Apache Spark that have led to its success include:

- Simple API
- Pluggable
- Support for many languages
- Distributed compute
- Fault Tolerant
- Lazy Evaluation of compute and transformations.

There are a number of distributed systems that may benefit from an interoperable data API, decoupling from exisiting systems like Apache Spark and Hadoop, like databases. By moving compute next to the data, we can not only simplify the system stack, but improve performance, security, safety, and resiliency.

## Goals

Create a resilient and distributable data API that will lead to a portable, host- and language-independent ecosystem of composable WebAssembly modules that can run distributed algorithms.

- Resiliency
- Enable AOT compilation
- Portable
- Host and language-independent
- Composable Wasm modules
- Highly performant distributed computation
- Write data pipelines in a familiar language
- Large-scale batch and streaming data processing
- Allow functions to operate on relations polymorphically, i.e. the columns and types may not be fixed when writing the function.
- Allow for new types, functions, operators, etc.

## Proposal

This design is focused on allowing a Wasm program to work with distributed data. This stands on the shoulders of other active proposals for Wasm and WASI including: interface types, modules, and shared-nothing linking. To represent distributed algorithms, wasi-data needs to provide a mechanism for a Wasm client to push a distributed query plan to it's host, and monitor that operation through to completion. There are many ways to achieve this goal, which is why we are still early in the design process for wasi-data. But here is what we are thinking so far:

First, we need a way to communicate a DAG of computation between client and host. We are evaluating multiple solutions to this problem:

1. Define the DAG as a [Substrait](https://substrait.io/) plan with extensions for defining custom operators and expressions.
2. Create our own tree datastructure that can be used to communicate a desired distributed operation from the client to host.
3. Rather than supporting an entire logical plan, provide an API for registering Wasm powered UDFs, TVFs, and UDAs. Then provide an API for executing SQL queries and either collecting them or streaming the result into a temporary table for followup computation.

Second, we need a way to make lambdas a first class concept in WASM. This means passing a function reference to the host in a way that allows the host to serialize and send that function (with all it's required state and dependencies) to another machine (or thread, or process) for execution. This allows a single WASM module to define everything it needs for a complete distributed operation. We are evaluating the idea of embedding each lambda function into the Wasm file as a separate module to achieve this goal.

## How does this relate to wasi-nn and wasi-parallel?

### wasi-nn

Some distributed operations may need to use wasi-nn to execute ML/AI algorithsm in a performant way. wasi-data may make additional optimizations to work well with wasi-nn, however it will mostly be a higher order concept.

### wasi-parallel

Similar to wasi-nn, some distributed operations may use wasi-parallel to process vectors of data in parallel. This could be especially powerful if wasi-parallel could gain partition local access to GPUs which is one of their goals. In this way, wasi-data and wasi-parallel will work together to define extremely high performance distributed algorithms which take advantage of whatever hardware is available in the cluster.

## Similar projects

The following libraries and frameworks expose API's that are similar in nature to wasi-data, and may also benefit from a language-agnostic API for distributed data computation:

- Pandas DataFrame
- Apache Flink DataStream and Windows
- Timely DataFlows
- Similar to distributed and pluggable runtime model of Apache Beam
  > “Dataflow aims to provide an abstraction layer between your code and the execution runtime”

## Future

The API for this proposal has not been solidified. It relies heavily on the interface types proposal and creating
iterators and generator types from that proposal. The MVP for this should be the MVP set of API's to create a
working computational DAG and could be proved out as a POC with an existing open source distributed system.
