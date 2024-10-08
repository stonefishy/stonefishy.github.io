---
title: 大数据存储格式之 Parquet, ORC, Avro
date: 2023-10-21 16:52:17
categories: [Big Data]
tags: [Apache Parquet, Apache ORC, Apache Avro]
---

数据存储格式在大数据处理和分析中起着至关重要的作用。Avro、Parquet 和 ORC（优化行列式）是 Hadoop 生态系统中使用的三种流行格式。每种格式都有其优势和独特功能，使其适合特定用例。

{% image /assets/images/big-data/apache-parquet-orc-avro.png, width=800px %}

## Apache Parquet
`Apache Parquet` 是一种列式存储格式，旨在优化大型数据集的查询性能和分析处理。它将数据组织成列，从而实现高效的压缩和列级操作。Parquet 文件存储有关架构的元数据和有助于查询优化的统计信息。该格式与 Apache Hadoop 生态系统高度兼容，包括 Apache Hive、Apache Impala 和 Apache Spark 等工具。

### 特点:
**列式存储**：数据按列进行存储，这使得对特定列的查询效率较高。
**支持压缩**：内置支持多种压缩算法，如 `Snappy`、`Gzip` 和 `Brotli`。
**支持复杂数据类型**：支持嵌套数据结构，如数组、映射等。
**兼容性**：广泛支持多种大数据处理框架，如 `Apache Spark`、`Apache Hive` 和 `AWS Athena`。

### 优点:
**高效查询**：列式存储提高了对特定列的查询性能，尤其是需要读取大量数据但只涉及少数列的情况。
**压缩率高**：由于相同列的数据在一起，能够更有效地压缩。
**适用于大数据处理**：优化了读取和存储性能。

### 缺点:
**写入性能**：相比于行式存储，写入性能可能较差，尤其是在数据写入时需要更多的编码和压缩操作。

### 使用场景:
- 适用于需要进行复杂分析和查询的大数据环境，如数据仓库和大数据分析系统。
- 适合需要高压缩比和快速读取的场景。

## Apache ORC (Optimized Row Columnar)
`Apache ORC` 是另一种专为基于 Hadoop 的大数据处理而设计的列式存储格式。它由 Apache Hive 项目开发，与其他格式相比，它提供了更好的性能和压缩能力。ORC 文件以高度优化的列式格式存储数据，可实现高效压缩、谓词下推和列级操作。

### 特点:
**列式存储**：像 `Parquet` 一样，`ORC` 也是列式存储。
**高压缩率**：ORC 提供高压缩率的支持，常用的压缩算法包括 `Zlib` 和 `LZ4`。
**内置索引**：支持内置的索引和统计信息，使得读取数据时更快。
**快速读取**：优化了读取性能，尤其是对于大数据量的查询。

### 优点:
**高效存储和压缩**：通常比 Parquet 提供更高的压缩比和更快的读取速度。
**内置索引**：通过索引加速了数据读取和查询。
**支持 Hive 特性**：与 `Apache Hive` 兼容性较好。

### 缺点:
**写入性能**：写入性能可能较低，尤其是在数据更新频繁的情况下。
**兼容性**：虽然支持的框架多，但与某些工具和系统的兼容性可能不如 Parquet 广泛。

### 使用场景:
- 适用于需要高压缩率和高效读取的大数据分析任务，尤其是在使用 `Apache Hive` 的环境中。
- 适合进行大量的批处理和分析任务。

## Apache Avro
`Apache Avro` 是一个基于行的数据序列化系统，专注于提供一种紧凑、高效且基于架构的数据序列化方法。它强调简单性并支持动态类型。Avro 以二进制格式存储数据，并在数据文件中包含架构。这种自描述功能允许轻松实现架构演变和跨不同编程语言的兼容性。

### 特点:
**行式存储**：数据以行的形式存储，每一行都是一个完整的记录。
**支持 schema**：使用 JSON 格式定义 schema，支持自描述数据。
**数据序列化**：主要用于数据的序列化和反序列化，适合流数据和消息传递系统。
**支持压缩**：支持多种压缩算法，如 Snappy。

### 优点:
**高效序列化**：优化了序列化和反序列化操作，适合流式处理。
**灵活的 schema 进化**：允许 schema 的演进，适合需要频繁更新 schema 的场景。
**简单易用**：自描述的 schema 使得数据交换变得简单。

### 缺点:
**查询性能**：行式存储可能在读取特定列时性能较差。
**压缩率**：通常不如 Parquet 和 ORC 高效。

### 使用场景:
- 适用于需要高效序列化和反序列化的流数据和消息传递系统，如 `Kafka`、`日志系统`等。
- 适合需要频繁更新 `schema` 和支持 schema 演进的场景。

## 总结
- `Parquet` 和 `ORC` 主要用于大数据存储和分析，因其列式存储特性提供了高效的查询和压缩性能。
- `Parquet` 适合广泛的工具和框架，尤其是在 `AWS Athena`、`Apache Spark` 等环境中。
- `ORC` 在 `Hadoop` 和 `Hive` 环境中表现尤为出色，适合需要高压缩率和读取性能的场景。
- `Avro` 主要用于数据的序列化和流数据处理，适合消息传递系统和需要高效序列化的应用, 适用于 `Kafka` 等流数据平台和需要高效数据交换的场景。