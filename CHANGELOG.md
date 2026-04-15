# Changelog

All notable changes to this fork are documented in this file.

This fork is based on [cjpais/Handy](https://github.com/cjpais/Handy).

## [Unreleased]

### Added
- Multi-part parallel download system for model files (`src-tauri/src/managers/model.rs`)
  - Downloads large model files using 4 concurrent HTTP Range requests
  - Each chunk is saved to a `.partN` file, enabling per-chunk resume on interruption
  - Server capabilities are probed via HEAD request before choosing download strategy
  - Falls back to single-stream download for servers without Range support or small files (<20 MB)
  - Chunks are merged into the final file after all complete, with automatic cleanup
- Unit tests for chunk calculation and merge logic
  - `test_calculate_chunks_large_file` — verifies correct splitting for 1.6 GB files
  - `test_calculate_chunks_small_file_single` — verifies single chunk for files under MIN_CHUNK_SIZE
  - `test_calculate_chunks_covers_entire_file` — verifies no byte gaps/overlaps
  - `test_calculate_chunks_medium_file` — verifies adaptive chunk count
  - `test_merge_chunks` — verifies correct concatenation and part file cleanup

### Changed
- `ModelManager::download_model()` — refactored to support both multi-part and single-stream strategies
- Partial size calculation now accounts for `.partN` chunk files (not just `.partial` files)
- Model deletion now cleans up leftover `.partN` files
