---
title: A fast in-process analytical database - DuckDB
date: 2023-11-19 15:48:56
categories: [Big Data]
tags: [DataBase, Parquet, DuckDB, Python]
---

## Overview
`DuckDB` is a seriously powerful database, with a host of unique and amazing features that are straight from the world of academic database research. It's a fast in-process analytical database that can handle large datasets and perform complex queries. It's `free`, `open-source`, and written in C++ and SQL. It supports to run on Linux, macOS, Windows, and all popular hardware architectures.

{% image /assets/images/big-data/duckdb-logo.png, width=600px %}

## Features
A few of key features for blazing fast OLAP query performance on large datasets (involving aggregations and joins on 100M+ rows on multiple tables), are listed below.

- Like other OLAP data warehouses, DuckDB uses `columnar storage`, which is a great fit for analytical workloads, because it allows for fast, efficient scans over large amounts of data
- Uses vectorized query execution, which is a technique that allows for processing large amounts of data in batches.
- Uses concurrent execution via threads, which allows for faster execution of queries, and is a great fit for modern multi-threaded CPUs.
- Offers a rich SQL dialect. It can read and write file formats such as `CSV`, `Excel`, `Parquet`, and `JSON`, to and from the `local file system` and `remote endpoints` such as S3 buckets.
- Supports integration with popular database such as `MySQL`, `PostgreSQL`, `SQLite`.
- Data can very easily move from a DuckDB table to a `Pandas` or `Polars` `DataFrame`, and `vice-versa`.
- It also offers connectors that allow users to directly read `Parquet` data from `AWS S3`, `GCS` and `Azure storage`.

{% image /assets/images/big-data/duckdb-analytic-workloads.png, width=800px, alt=A productive DuckDB setup for large (100M+ size) analytical workloads %}

## Installation
### As Software
DuckDB can be installed on `Linux`, `macOS`, and `Windows`. It can be installed using a package manager, or by downloading the source code and building it manually. You can refer to the official documentation for installation instructions. 

### As Library
`DuckDB` not only as tools or software on system. It also supports as a library that can be used in other applications, as a part of a larger application, or as a standalone database. It can be used in `Python`, `Node.js`, `R`, `Java`, `C++`, and other programming languages.

#### Python

``` shell
pip install duckdb --upgrade
```

#### Node.js

``` shell
npm install duckdb
```

#### Java

``` xml
<dependency>
    <groupId>org.duckdb</groupId>
    <artifactId>duckdb_jdbc</artifactId>
    <version>1.0.0</version>
</dependency>
```

## Usage

### Supports Various File Formats
For the DuckDB usage, below is using `Python` as an example.
``` python
import duckdb
duckdb.read_csv("example.csv")                # read a CSV file into a Relation
duckdb.read_parquet("example.parquet")        # read a Parquet file into a Relation
duckdb.read_json("example.json")              # read a JSON file into a Relation

duckdb.sql("SELECT * FROM 'example.csv'")     # directly query a CSV file
duckdb.sql("SELECT * FROM 'example.parquet'") # directly query a Parquet file
duckdb.sql("SELECT * FROM 'example.json'")    # directly query a JSON file

duckdb.sql("SELECT 42").write_parquet("out.parquet") # Write to a Parquet file
duckdb.sql("SELECT 42").write_csv("out.csv")         # Write to a CSV file
duckdb.sql("COPY (SELECT 42) TO 'out.parquet'")      # Copy to a Parquet file

duckdb.sql("SELECT 42").fetchall()   # Python objects
duckdb.sql("SELECT 42").df()         # Pandas DataFrame
duckdb.sql("SELECT 42").pl()         # Polars DataFrame
duckdb.sql("SELECT 42").arrow()      # Arrow Table
duckdb.sql("SELECT 42").fetchnumpy() # NumPy Arrays
```

### Supports Multiple Data Sources
It also supports various data sources, including local files, S3 buckets, and remote endpoints.

``` SQL 
SELECT AVG(trip_distance) FROM 's3://yellow_tripdata_20[12]*.parquet'
SELECT * FROM '~/local/files/file.parquet'
SELECT * FROM 'https://shell.duckdb.org/data/tpch/0_01/parquet/lineitem.parquet'
```

### Pandas DataFrames
DuckDB can even treat pandas dataframes as DuckDB tables and query them directly.
``` python
import pandas as pd
import duckdb

mydf = pd.DataFrame({'a' : [1, 2, 3]})
print(duckdb.query("SELECT sum(a) FROM mydf;").fetchall())
``` 

### Retional API
It also supports retional API for querying data, which allows for filtering, grouping, and aggregating data.

``` python
import duckdb
rel = duckdb.sql("SELECT * FROM range(1_000_000) tbl(id)")
duckdb.sql("SELECT sum(id) FROM rel").show()

# aggregate(expr, groups = {})
rel = duckdb.sql("SELECT * FROM range(1_000_000) tbl(id)")
rel.aggregate("id % 2 AS g, sum(id), min(id), max(id)")

# except_(rel)
r1 = duckdb.sql("SELECT * FROM range(10) tbl(id)")
r2 = duckdb.sql("SELECT * FROM range(5) tbl(id)")
r1.except_(r2).show()

# filter(condition)
rel = duckdb.sql("SELECT * FROM range(1_000_000) tbl(id)")
rel.filter("id > 5").limit(3).show()

# there are more operations available, refer to the documentation for more details.
```

### Result Output
`DuckDB` query result output can be saved on local file system by using below methods.
- write_csv
- write_parquet

### Persistence
The data also can be persistent on disk as `*.db` file. The duckdb.connect(dbname) creates a connection to a persistent database. Any data written to that connection will be persisted, and can be reloaded by reconnecting to the same file, both from Python and from other DuckDB clients.

``` python
import duckdb

# create a connection to a file called 'file.db'
con = duckdb.connect("file.db")
# create a table and load data into it
con.sql("CREATE TABLE test (i INTEGER)")
con.sql("INSERT INTO test VALUES (42)")
# query the table
con.table("test").show()
# explicitly close the connection
con.close()
# Note: connections also closed implicitly when they go out of scope
```

Or using context manager to automatically close the connection.

``` python
import duckdb

with duckdb.connect("file.db") as con:
    con.sql("CREATE TABLE test (i INTEGER)")
    con.sql("INSERT INTO test VALUES (42)")
    con.table("test").show()
    # the context manager closes the connection automatically
```

## Conclusion
`DuckDB` is a great database for analytical workloads, with a rich SQL dialect, fast vectorized query execution, and support for various file formats. It's also easy to use as a library in other applications, and can be used as a standalone database. Happy to try!
