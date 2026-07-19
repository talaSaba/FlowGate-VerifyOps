# FlowGate VerifyOps

FlowGate VerifyOps is a Linux networking software and verification project.

The project will combine:

- a C++ network-processing product;
- a Python and PyTest verification framework;
- functional, integration, system and performance testing;
- failure injection and automatic diagnostics;
- Docker-based test environments;
- CI/CD and nightly regression testing.

## Current status

Phase 0: Project environment and build system.

## Build

```bash
cmake -S . -B build -G Ninja
cmake --build build