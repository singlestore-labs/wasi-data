# Motivation

Support for highly parallel algorithms and distributed computation is key for operating on "big data" of modern data pipelines. There are a number of frameworks and derivations of MapReduce, but few are language-agnostic.

Apache Spark is one such example. The main abstraction Spark provides is a resilient distributed dataset (RDD), which is a fault-tolerant collection of elements that can be operated on in parallel.

The attributes of Apache Spark that have led to its success include:

- Simple API
- Pluggable
- Support for many languages
- Distributed compute
- Fault Tolerant
- Lazy Evaluation of compute and transformations.

There are a number of distributed systems that may benefit from an interoperable data API, decoupling from exisiting systems like Apache Spark and Hadoop, like databses. By moving compute next to the data, we can not only simplify the system stack, but improve performance, security, safety, and resiliency.

If an API existed within the WASM ecosystem for resilient and distributable data frames, it would enable portable, host- and language-independent ecosystem of composable WebAssembly modules to support MapReduce and other distributed algorithms.

## Goals

- Resiliency is a key feature of distributed computation. Spark to persist an RDD in memory, allowing it to be reused efficiently across parallel operations. Finally, RDDs automatically recover from node failures.
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

This design builds on active proposals for WASM and WASI like interface types for handles and records, shared-nothing linking, and others to create declared, strongly-typed, and sandboxed WASM modules for runtimes to distribute computation across systems.

> A shared-nothing architecture partitions a whole application into multiple isolated units that encapsulate their mutable state; shared mutable state is either banned or significantly restricted. When multiple languages are used, then, it's natural to put separate languages into separate isolated units. In raw WebAssembly terms, a natural shared-nothing unit is a module which only imports and exports functions and immutable values, but not memories, tables or mutable globals.

Interface-types allow a wide variety of value representations, avoiding the need for intermediate (de)serialization steps. Ideally, this will even include lazily-generated data structures (e.g., via iterators, generators or comprehensions).

runtime responsible for maintaining the computational DAG
RDD<Row<A,B,C…>>

map(func (Row<A,B,C>) Row<…>)

mapPartitions(func (iter<Row<A,B,C>>) iter<Row<…>>)

reduce(memo Row<output>, func(memo Row<output>, input Row<original>) memo Row<output>)

createDataFrame([
    Row(a=1, b=2., c='string1', d=date(2000, 1, 1), e=datetime(2000, 1, 1, 12, 0)),
    Row(a=2, b=3., c='string2', d=date(2000, 2, 1), e=datetime(2000, 1, 2, 12, 0)),
    Row(a=4, b=5., c='string3', d=date(2000, 3, 1), e=datetime(2000, 1, 3, 12, 0))
])spark.createDataFrame([
    (1, 2., 'string1', date(2000, 1, 1), datetime(2000, 1, 1, 12, 0)),
    (2, 3., 'string2', date(2000, 2, 1), datetime(2000, 1, 2, 12, 0)),
    (3, 4., 'string3', date(2000, 3, 1), datetime(2000, 1, 3, 12, 0))
], schema='a long, b double, c string, d date, e timestamp')

## How does this relate to wasi-nn?

Some data transformations may run ML against a given data frame.

The API of wasi-nn will allow for hardware acceleration of various implementations.
This is key for ML workloads.
A wasm module that runs ML inferncing on a data frame, may also rely on wasi-nn API for acceleration.

Potentially wasi-nn's tensor_data could instead be a data frame.

Benefits of WASI-NN and WASI-crypto proposals:

The API should also not leak internal data structures. For example, there shouldn't be any ways to get the internal state of a cipher or an ML algorithm.

As the dominant platform and ecosystem for a currency, we know that security will be highly scrutinized and evolve.

Absence of undefined behaviors

All handle types MUST be thread-safe.

Secret managment

Highly performant ML with hardware support


## How does this relate to wasi-crypto?

The concepts around safety and security for the motivations behind wasi-crypto are relevant for wasi-data.


## Similar projects

- Pandas DataFrame
- Apache Flink DataStream and Windows
- Timely DataFlows
- Similar to distributed and pluggable runtime model of Apache Beam
    “Dataflow aims to provide an abstraction layer between your code and the execution runtime,”

https://dev.materialize.com/api/rust/transform/trait.Transform.html



Libraries and frameworks that may benefit from a language-agnostic API for distributed data computation:
- Apache Flink
-

## Future

Where can we go from here?

Support for graph data structures like RDF’s, the basis of the semantic web. The most straightforward data structure for representing an RDF graph or dataset is to store triples or quads with their constituting terms directly in the data structure.

Consolidation of complex systems to composable integrations across language barriers

wasi-sql

support for data formats like avro and arrow

## TODO

- include avro type?
- include create a source connector API for postgres, kafka, etc?
- wasi-sql?? https://dev.materialize.com/api/rust/sql/all.html

## Tags

- Structured streaming