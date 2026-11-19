# rust-file-compressor

My first Rust project: a small command-line file compressor.

It reads any file and writes a **gzip-compressed** version using the
[`flate2`](https://crates.io/crates/flate2) crate, then prints the input size,
output size, and elapsed time.

## How it works

```rust
let input  = BufReader::new(File::open(source)?);
let output = File::create(target)?;
let mut encoder = GzEncoder::new(output, Compression::default());
copy(&mut input, &mut encoder)?;
let output = encoder.finish()?;
```

The file is streamed through a buffered reader into the encoder, so memory use
stays low even for large inputs. `finish()` flushes the gzip trailer.

## Build

Requires Rust (rustup) and Cargo:

```powershell
cargo build
```

## Run

```powershell
cargo run -- input.txt output.gz
```

Or use the compiled binary directly:

```powershell
.\target\debug\rust-file-compressor.exe input.txt output.gz
```

Example output:

```
source len : 3405
Target len : 83
Elapsed: 307.5µs
```

To decompress the result:

```powershell
gzip -d output.gz
```

## Notes

- The first argument is the source file, the second is the destination file.
- Compressed output and sample input files are deliberately not committed;
  `.gitignore` excludes `target/`, `book.PDF`, and `compressed_file`.
- This was written while learning Rust — the error handling uses `unwrap()`,
  so a missing source file will panic. Improving that is a good next exercise.
