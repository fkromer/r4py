# Build pipelines

## Public Rust-only code Python package: GitHub -> PyPi

A good example of a Python package public-ally deployed to [PyPi](https://pypi.org/) is [uuidt](https://pypi.org/project/uuidt/).
The source of the project on GitHub [isaacharrisholt/uuidt](https://github.com/isaacharrisholt/uuidt) contains Rust code in [`uuidt.src.lib.rs`](https://github.com/isaacharrisholt/uuidt/tree/main/src).
There is no Python source code contained at all.
The Python package is built for MacOS, several Linux architectures and Windows.

``` rust title="uuidt/src/lib.rs" hl_lines="5 27 35 61 91 92 94 119"
#![allow(clippy::upper_case_acronyms)]

use std::u128;
use gethostname::gethostname;
use pyo3::prelude::*; // (1)
use radix_fmt::radix_36;
use rand::{distributions::Alphanumeric, Rng};

fn string_radix_36(string: String, length: usize) -> String {
    format!(
        "{:0>length$}",
        format!(
            "{}",
            radix_36(string.into_bytes().iter().map(|&b| b as u16).sum::<u16>())
        )
        .to_lowercase()
    )
}

fn timestamp_radix_36(timestamp: u128) -> (String, String) {
    let timestamp_string = format!("{}", radix_36(timestamp as u64)).to_lowercase();
    let part_1 = timestamp_string[0..8].to_string();
    let part_2 = timestamp_string[8..12].to_string();
    (part_1, part_2)
}

#[pyclass(frozen, module = "uuidt", get_all, freelist = 10)] // (2)
struct UUIDT {
    namespace: String,
    timestamp: u128,
    hostname: String,
    random_chars: String,
}

#[pymethods] // (3)
impl UUIDT {
    #[new]
    fn new() -> PyResult<Self> {
        Err(PyErr::new::<pyo3::exceptions::PyNotImplementedError, _>(
            "Cannot instantiate UUIDT directly. Use uuidt() instead.",
        ))
    }

    fn __str__(&self) -> PyResult<String> {
        let (timestamp_part_1, timestamp_part_2) = timestamp_radix_36(self.timestamp);
        let namespace_radix_36 = string_radix_36(self.namespace.clone(), 4);
        let hostname_radix_36 = string_radix_36(self.hostname.clone(), 4);

        Ok(format!(
            "{}-{}-{}-{}-{}",
            timestamp_part_1,
            timestamp_part_2,
            namespace_radix_36,
            hostname_radix_36,
            self.random_chars
        ))
    }
}

/// Creates a new UUIDT object.
#[pyfunction] // (4)
fn new(namespace: String) -> PyResult<UUIDT> {
    if namespace.is_empty() {
        return Err(PyErr::new::<pyo3::exceptions::PyValueError, _>(
            "Namespace cannot be empty.",
        ));
    }

    let hostname = gethostname().into_string().unwrap();
    let timestamp = std::time::SystemTime::now()
        .duration_since(std::time::UNIX_EPOCH)
        .unwrap()
        .as_nanos();

    let random_chars = rand::thread_rng()
        .sample_iter(&Alphanumeric)
        .take(12)
        .map(char::from)
        .collect::<String>()
        .to_lowercase();

    Ok(UUIDT {
        namespace,
        timestamp,
        hostname,
        random_chars,
    })
}

/// Extract the timestamp from a UUIDT string.
#[pyfunction] // (5)
fn extract_timestamp(uuidt: String) -> PyResult<u128> { // (6)
    if uuidt.is_empty() {
        return Err(PyErr::new::<pyo3::exceptions::PyValueError, _>( // (7)
            "UUIDT cannot be empty.",
        ));
    }

    let uuidt_parts: Vec<&str> = uuidt.split('-').collect();
    if uuidt_parts.len() != 5 {
        return Err(PyErr::new::<pyo3::exceptions::PyValueError, _>(
            "Invalid UUIDT.",
        ));
    }

    let timestamp_part_1 = uuidt_parts[0];
    let timestamp_part_2 = uuidt_parts[1];

    let timestamp = format!("{}{}", timestamp_part_1, timestamp_part_2);
    match u128::from_str_radix(&timestamp, 36) {
        Ok(timestamp) => Ok(timestamp),
        Err(_) => Err(PyErr::new::<pyo3::exceptions::PyValueError, _>(
            "Invalid UUIDT.",
        )),
    }
}

/// Timestamp-orderable UUIDs for Python, written in Rust.
#[pymodule] // (8)
fn uuidt(_py: Python, m: &PyModule) -> PyResult<()> {
    m.add_function(wrap_pyfunction!(new, m)?)?;
    m.add_function(wrap_pyfunction!(extract_timestamp, m)?)?;
    m.add_class::<UUIDT>()?;
    Ok(())
}
```

1.  [pyo3::prelude](https://pyo3.rs/main/doc/pyo3/prelude/index.html#) - The purpose of this module is to alleviate imports of many commonly used items of the PyO3 crate by adding a glob import to the top of pyo3 heavy modules.
2.  [`#[pyclass]`](https://pyo3.rs/main/class) - Exposes the Rust struct `UUIDT` as Python class.
3.  [`#[pymethods]`](https://pyo3.rs/main/class#instance-methods) - Exposes Rust [methods](https://doc.rust-lang.org/book/ch05-03-method-syntax.html#defining-methods) of struct `UUIDT` as Python class methods.
4.  Exposes the Rust function as Python function.
5.  Exposes the Rust function as Python function.
6.  `PyResult` implements the Python function return.
7.  `PyValueError` implements a Python `ValueError` exception.
8.  [`#[pymodule]`](https://pyo3.rs/main/module#python-modules) exposes a Python module.

[GitHub Actions](https://docs.github.com/en/actions) are used as CI system to build the Python packages for the different operating systems and architectures.
The Rust code is built into a Python package providing a Python API using [maturin](https://github.com/PyO3/maturin) (via [`PyO3/maturin-action@v1`](https://github.com/PyO3/maturin-action)).

``` yaml title=".github/workflows/uuidt.yml" hl_lines="12 15-16 19 26 28 31 33 38-39 42 48 50 52 56 61-62 65 72 74 78 98 100-101 103 107 111"
name: "Release uuidt"

on:
  workflow_dispatch:
  release:
    types: [published]

permissions:
  contents: read

env:
  PYTHON_VERSION: 3.7

jobs:
  linux:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        target: [x86_64, x86, aarch64, armv7, s390x, ppc64le]
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: ${{ env.PYTHON_VERSION }}
      - name: Build wheels
        uses: PyO3/maturin-action@v1
        with:
          target: ${{ matrix.target }}
          args: --release --out dist --find-interpreter
          sccache: 'true'
          manylinux: auto
      - name: Upload wheels
        uses: actions/upload-artifact@v3
        with:
          name: wheels
          path: dist

  windows:
    runs-on: windows-latest
    strategy:
      matrix:
        target: [x64, x86]
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: ${{ env.PYTHON_VERSION }}
          architecture: ${{ matrix.target }}
      - name: Build wheels
        uses: PyO3/maturin-action@v1
        with:
          target: ${{ matrix.target }}
          args: --release --out dist --find-interpreter
          sccache: 'true'
      - name: Upload wheels
        uses: actions/upload-artifact@v3
        with:
          name: wheels
          path: dist

  macos:
    runs-on: macos-latest
    strategy:
      matrix:
        target: [x86_64, aarch64]
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: ${{ env.PYTHON_VERSION }}
      - name: Build wheels
        uses: PyO3/maturin-action@v1
        with:
          target: ${{ matrix.target }}
          args: --release --out dist --find-interpreter
          sccache: 'true'
      - name: Upload wheels
        uses: actions/upload-artifact@v3
        with:
          name: wheels
          path: dist

  sdist:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build sdist
        uses: PyO3/maturin-action@v1
        with:
          command: sdist
          args: --out dist
      - name: Upload sdist
        uses: actions/upload-artifact@v3
        with:
          name: wheels
          path: dist

  release:
    name: Release
    runs-on: ubuntu-latest
    needs: [linux, windows, macos, sdist]
    steps:
      - uses: actions/download-artifact@v3
        with:
          name: wheels
      - name: Publish to PyPI
        uses: PyO3/maturin-action@v1
        env:
          MATURIN_PYPI_TOKEN: ${{ secrets.PYPI_API_TOKEN }}
        with:
          command: upload
          args: --skip-existing *
```

Finally the package is uploaded to [PyPi](https://pypi.org/) &rarr; [project/uuidt](https://pypi.org/project/uuidt/).