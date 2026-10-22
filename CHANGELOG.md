# Changelog

All notable changes to this project are documented in this file.
The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project
uses [Semantic Versioning](https://semver.org/).

## [2.0.0] - 2026-10-22

First public release of the repackaged toolkit.

### Changed
- Release assets are named `pdd-toolkit-linux-x86_64.tar.gz` and
  `pdd-toolkit-windows-x86_64.zip`.
- The Linux installer creates the command `pdd-toolkit`.
- The installer removes any previous installation before extracting the new one.
- The self-test uses a synthetic curve generated at runtime, so no data file is needed.
- CI fails if any spreadsheet or data file is tracked in the repository.

### Removed
- Bundled sample data. See the README for the expected CSV format.
- The old install guide for the former Streamlit interface.
