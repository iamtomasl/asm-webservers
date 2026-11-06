# asm-webservers

The same small HTTP/1.1 server implemented with different I/O models, in
x86-64 assembly (NASM, Linux, no libc), to compare how each model behaves
under load.

Every model shares one protocol library (parser, router, response builder)
and differs only in how it handles connections and I/O.

## Layout

| Path               | Contents                                                   |
|--------------------|------------------------------------------------------------|
| `common/platform/` | Syscall wrappers and logging; the only place syscalls live |
| `common/http/`     | HTTP parser, router and response builder (no I/O)          |
| `common/handlers/` | Endpoint handlers, returning response plans                |
| `models/`          | One subdirectory per I/O model                             |
| `tests/`           | Parser test vectors and HTTP smoke tests                   |
| `fuzz/`            | AFL++ harness for the parser                               |
| `bench/`           | wrk2 benchmark workloads                                   |
| `www/`             | Static files served by `/static`                           |
| `docs/`            | Benchmark results and observations                         |

## Models

| Model                 | Idea                                          | Status  |
|-----------------------|-----------------------------------------------|---------|
| `01-iterative`        | One connection at a time                      | planned |
| `02-fork`             | One process per connection                    | planned |
| `03-prefork`          | Fixed pool of processes                       | planned |
| `04-threads`          | One thread per connection                     | planned |
| `05-poll`             | Single-threaded event loop with `poll`        | planned |
| `06-epoll`            | Single-threaded event loop with `epoll`       | planned |
| `07-epoll-percore`    | One `epoll` loop per pinned core              | planned |
| `08-io_uring`         | Single-threaded completion loop with io_uring | planned |
| `09-io_uring-percore` | One io_uring per pinned core                  | planned |

## Endpoints

| Endpoint             | Purpose                                         |
|----------------------|-------------------------------------------------|
| `GET /plaintext`     | Minimal response; measures per-request overhead |
| `GET /static/<name>` | Serves a file from `www/`                       |
| `GET /large`         | Large body; exercises partial writes            |
| `GET /delay?ms=N`    | Responds after N ms; simulates a slow backend   |
| `GET /cpu?n=N`       | CPU-bound work before responding                |
| `POST /echo`         | Returns the request body                        |
| `GET /stats`         | Request and connection counters                 |

## Requirements

NASM 3.x, GNU binutils (`ld`, `ar`), make, gdb, curl, AFL++ and wrk2.
