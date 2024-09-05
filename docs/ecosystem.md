# Ecosystem

## Cheat Sheet

### Tooling

W.r.t. tooling the Rust ecosystem seems to be more consistent than the Python ecosystem.

| Purpose | Python | Rust |
| ------- | ------ | ---- |
| Package Management | [`uv`](https://github.com/astral-sh/uv) | [`cargo`](https://github.com/rust-lang/cargo) |
| SBOM Generation (SPDX) | ? | `cargo spdx` via [cargo-spdx](https://github.com/alilleybrinker/cargo-spdx) |
| SBOM Generation (CycloneDX) | ? | `cargo cyclonedx` via [cargo-cyclonedx](https://github.com/CycloneDX/cyclonedx-rust-cargo/blob/main/cargo-cyclonedx/README.md) |
| Linting (code) | [`ruff check`](https://docs.astral.sh/ruff/linter/#ruff-check) (via [linter rule selection](https://docs.astral.sh/ruff/linter/) for a lot of drop-in tool replacements) | [`cargo clippy`](https://doc.rust-lang.org/clippy/usage.html#cargo-subcommand) (via [clippy](https://github.com/rust-lang/rust-clippy)) |
| Formatting (syntax) | [`ruff format`](https://docs.astral.sh/ruff/formatter/) ([in a black compatible manner](https://docs.astral.sh/ruff/formatter/#black-compatibility)) | `cargo fmt` (via [rustfmt](https://github.com/rust-lang/rustfmt)) |
| Formatting (imports) | [`ruff format`](https://github.com/astral-sh/ruff) (via [`select = ["I"]`](https://docs.astral.sh/ruff/rules/#isort-i)) | [`cargo fmt`](https://github.com/rust-lang/rustfmt) (via [rustfmt](https://github.com/rust-lang/rustfmt)) |
| Formatting (docstring code examples) | [`ruff format`](https://docs.astral.sh/ruff/formatter/#docstring-formatting) | ? |
| Static Type Checking | [`mypy`](https://github.com/python/mypy) | (not required, handled by compiler) |
| Package Documentation Generation | [mkdocs-material](https://squidfunk.github.io/mkdocs-material/) or [sphinx](https://www.sphinx-doc.org/) | [`cargo doc`](https://doc.rust-lang.org/cargo/commands/cargo-doc.html)/[`cargo rustdoc`](https://doc.rust-lang.org/cargo/commands/cargo-rustdoc.html) |
| Package API Generation | [mkdocs-material](https://squidfunk.github.io/mkdocs-material/) + [mkdocstrings](https://mkdocstrings.github.io/) or [sphinx](https://www.sphinx-doc.org/) + [sphinx.ext.autodoc](https://www.sphinx-doc.org/en/master/usage/extensions/autodoc.html) | [`cargo doc`](https://doc.rust-lang.org/cargo/commands/cargo-doc.html)/[`cargo rustdoc`](https://doc.rust-lang.org/cargo/commands/cargo-rustdoc.html) |

### IDEs

| Purpose | Python | Rust |
| ------- | ------ | ---- |
| Full Blown | [PyCharm](https://www.jetbrains.com/pycharm/) | [RustRover](https://www.jetbrains.com/rust/) |
| [VSCode](https://github.com/microsoft/vscode) | Extensions: [ms-python.python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) | Extensions: [rust-lang.rust-analyzer](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer) |
| [Emacs](https://www.gnu.org/software/emacs/) | [Elpy](https://github.com/jorgenschaefer/elpy) | [rust-analyzer](https://rust-analyzer.github.io/manual.html#emacs) |
| [Vim](https://www.vim.org/) | [vim-lsp](https://github.com/prabirshrestha/vim-lsp) | [rust-analyzer](https://rust-analyzer.github.io/manual.html#vimneovim) |
| [Zed](https://zed.dev/) | natively via [tree-sitter-python](https://github.com/tree-sitter/tree-sitter-python) and [pyright](https://github.com/microsoft/pyright) | natively via [tree-sitter-rust](https://github.com/tree-sitter/tree-sitter-rust) and [rust-analyzer](https://rust-analyzer.github.io/manual.html#zed) |

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
| Code Coverage Checking | [coverage](https://github.com/nedbat/coveragepy) | [tarpaulin](https://github.com/xd009642/tarpaulin) |
| Property Based Testing | [hypothesis](https://github.com/HypothesisWorks/hypothesis) | [proptest](https://github.com/proptest-rs/proptest) |
| Snapshot Testing | [syrupy (pytest plugin)](https://github.com/syrupy-project/syrupy) |[insta](https://github.com/mitsuhiko/insta) |