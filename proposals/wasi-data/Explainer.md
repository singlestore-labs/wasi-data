# Motivation

Support for [embarrassingly parallel](https://en.wikipedia.org/wiki/Embarrassingly_parallel) parallel algorithms and distributed computation is key for operating on "big data" of modern data pipelines. There are a number of frameworks and derivations of MapReduce, but few are language-agnostic.

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

Create a resilient and distributable data API that will lead to a portable, host- and language-independent ecosystem of composable WebAssembly modules that support MapReduce and other embarrassingly parallel algorithms.

- Resiliency
- Enable AOT compilation
- Portable
- Host and language-independent
- Composable WASM modules
- Highly performant distributed computation
- Write data pipelines in a familiar language
- Large-scale batch and streaming data processing
- Allow functions to operate on relations polymorphically, i.e. the columns and types may not be fixed when writing the function.
- Allow for new types, functions, operators, etc.

## Proposal

This design builds on active proposals for WASM and WASI like interface types for handles and records, shared-nothing linking, and others to create declared, strongly-typed, and sandboxed WASM modules for runtimes for [embarrassingly parallel](https://en.wikipedia.org/wiki/Embarrassingly_parallel) algorithms.

> A shared-nothing architecture partitions a whole application into multiple isolated units that encapsulate their mutable state; shared mutable state is either banned or significantly restricted. When multiple languages are used, then, it's natural to put separate languages into separate isolated units. In raw WebAssembly terms, a natural shared-nothing unit is a module which only imports and exports functions and immutable values, but not memories, tables or mutable globals.

Interface-types allow a wide variety of value representations, avoiding the need for intermediate (de)serialization steps. Ideally, this will even include lazily-generated data structures (e.g., via iterators, generators or comprehensions).

Implementations of the map-reduce paradigm requires a way to connect the processes performing the map and reduce phases. Because this is for massive data, the various runtimes may have unique and domain specific methods
for managing the data, e.g. distributed file system, data sharding, etc. This means that the runtime must
be responsible for maintaining the computational DAG.

```bash
# TODO create the acutal API, we know that it will have traits like map, reduce, join
# We will need to be able to create windows and function closures
DataSet<Row<A,B,C…>>

map(func (Row<A,B,C>) Row<…>)

reduce(Row<out>, func(Row<out>, Row<orig>) Row<out>)
```

## How does this relate to wasi-nn?

Some data transformations may run ML against a given data frame.

The API of wasi-nn will allow for hardware acceleration of various implementations.
This is key for ML workloads.
A wasm module that runs ML inferencing on a data frame, may also rely on wasi-nn API for acceleration.
Potentially wasi-nn's tensor_data could instead be a data frame.

## Similar projects

The following libraries and frameworks expose API's that are similar in nature to wasi-data, and may also benefit from a language-agnostic API for distributed data computation:

- Pandas DataFrame
- Apache Flink DataStream and Windows
- Timely DataFlows
- Similar to distributed and pluggable runtime model of Apache Beam
  > “Dataflow aims to provide an abstraction layer between your code and the execution runtime”

## Future

The API for this proposal has not been solidified. It relies heavily on the interface types proposal and creating
iterators and generator types from that proposal. The MVP for this should be the MVP set of API's to creat a
working computational DAG and could be proved out as a POC with an existing framework (e.g. Timely Dataflow).

Something not yet discussed, is support for graph data structures like RDF’s, the basis of the semantic web. The most straightforward data structure for representing an RDF graph or dataset is to store triples or quads with their constituting terms directly in the data structure.

Additional API's may build on this, such as a future proposal for `wasi-sql`.

Creating interface type libraries for wasm modules to support additional data formats like avro and arrow.
