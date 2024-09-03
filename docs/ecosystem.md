# Ecosystem

The Rust ecosystem is w.r.t. tooling pretty consistent. The Python ecosystem is more fragmented (especially when it comes to package management).

## Cheatsheet

### Tooling

| Purpose | Python | Rust |
| ------- | ------ | ---- |
| Package Management | [uv](https://github.com/astral-sh/uv) | [cargo](https://github.com/rust-lang/cargo) |
| Formatting | [ruff](https://github.com/astral-sh/ruff) | [rustfmt (cargo fmt)](https://github.com/rust-lang/rustfmt) |
| Static type Checking | [mypy](https://github.com/python/mypy) | (not required, does compiler) |

### Debugging

| Purpose | Python | Rust |
| ------- | ------ | ---- |
| Poor Man's Debugging | [pysnooper](https://github.com/cool-RR/PySnooper) |  |

### Logging

| Purpose | Python | Rust |
| ------- | ------ | ---- |
| Logging | [loguru](https://github.com/Delgan/loguru) | [log](https://github.com/rust-lang/log) |
| Structured Logging | [structlog](https://github.com/hynek/structlog) | [slog](https://github.com/slog-rs/slog) |
| Application Level Tracing | ? | [tracing](https://github.com/tokio-rs/tracing) |

### Testing

| Purpose | Python | Rust |
| ------- | ------ | ---- |
| Unit Testing | [pytest](https://github.com/pytest-dev/pytest) | (cargo test) |
| Code Coverage Checking |  | [tarpaulin](https://github.com/xd009642/tarpaulin) |
| Property Based Testing | [hypothesis](https://github.com/HypothesisWorks/hypothesis) | [proptest](https://github.com/proptest-rs/proptest) |
| Snapshot Testing | [syrupy (pytest plugin)](https://github.com/syrupy-project/syrupy) |[insta](https://github.com/mitsuhiko/insta) |