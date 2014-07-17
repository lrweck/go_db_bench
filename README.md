# Go Database Benchmark

This tests the performance of [pgx native](https://github.com/jackc/pgx), [pgx
through database/sql](https://github.com/jackc/pgx/stdlib),
[pq](https://github.com/lib/pq) through database/sql, [go-pg](github.com/go-
pg/pg),  and theoretical maximum PostgreSQL performance.

## Configuration

go_db_bench reads its configuration from the environment.

    GO_DB_BENCH_PG_HOST - defaults to localhost
    GO_DB_BENCH_PG_USER - default to OS user
    GO_DB_BENCH_PG_PASSWORD - defaults to empty string
    GO_DB_BENCH_PG_DATABASE - defaults to go_db_bench

## Core Benchmarks

go_db_bench includes tests selecting one value, one row, and multiple rows. It
executes these queries with and without prepared statements.

Example execution:

    GO_DB_BENCH_PG_HOST=/private/tmp go test -test.bench=. -test.benchmem

## HTTP Benchmarks

go_db_bench includes a simple HTTP server that serves JSON directly from
PostgreSQL. This allows testing the performance of database drivers in a more
real-world environment.

Example execution:

    go build && GO_DB_BENCH_PG_HOST=/private/tmp ./go_db_bench

It exposes the following endpoints:

* /people/pgx-native - pgx through its native interface
* /people/pgx-stdlib - pgx through database/sql
* /people/pq - pq through database/sql

Start the server and use your favorite HTTP load tester to benchmark (I
recommend [siege](http://www.joedog.org/siege-home/) or
[overload](https://github.com/jackc/overload)).

## Theoretical Max PostgreSQL Performance

This benchmark includes a minimum PostgreSQL driver sufficient to establish a
connection and prepare statements. Query execution is benchmarked by sending a
[]byte filled with the query command and reading until the ready for query
message is received. This should be the theoretical best performance a Go
PostgreSQL driver could achieve.

Caveat: The returned data is not checked or parsed. It is only read until the
ready for query message is received. If an error occurs it may not be apparent
which could cause the timing to be misleading.
