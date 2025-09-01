# Mini Docker - Miniature Container Runtime in Rust

***

## Overview

Mini Docker is a lightweight container runtime implemented in Rust that demonstrates key containerization technologies including process isolation with Linux namespaces, resource limitation using cgroups v2, container lifecycle management, layered container images, and basic container networking.

It serves as a learning and experimental platform for container runtime development and systems programming in Rust. Mini Docker is modular by design, extensible, and implements production-quality features that rival industry runtimes in core functionality.

***

## About This Project

Mini Docker is a fully functional container runtime built from scratch with Rust focusing on deep integration with Linux kernel features. It emphasizes modern async programming, resource management, and container orchestration fundamentals.

This project serves both as an educational tool and a robust foundation for exploring container technology and namespaces, resource control, persistence, and networking.

***

## Features

- Container process isolation via Linux namespaces
- Resource limits (memory, CPU) enforced with cgroups v2
- CLI commands for starting, stopping, listing, removing containers
- Layered image management supporting container filesystem snapshots
- Network bridge creation with automatic fallback to host networking on WiFi
- Persistent container state tracking across multiple runtime executions
- Async concurrency with Tokio for real-time container monitoring
- Modular architecture for easy extensibility and learning

***

## Requirements

- Linux system (kernel 4.15+ recommended)
- Rust toolchain 1.70+
- Root permissions for cgroups and network operations
- Linux utilities: `bridge-utils`, `iptables`, `iproute2`
- Recommended: Ubuntu or Debian-based distros for smooth networking setup

***

## Usage

### Building the Alpine Base Image

Create the Alpine image used as container root filesystem by running the provided script:

```bash
./scripts/create_alpine_image.sh
```

This script downloads a minimal Alpine rootfs tarball and prepares it for container use.

### Running a Container

```bash
cargo run run --name my-container ./images/alpine /bin/sh -c "echo Hello, container!"
```

### Listing Containers

- Running containers only:

```bash
cargo run ps
```

- All containers including stopped:

```bash
cargo run ps --all
```

### Stopping a Container

```bash
cargo run stop my-container
```

### Removing a Container

```bash
cargo run remove my-container
```

### Networking Demo

```bash
cargo run --examples networking_demo
```

***

## System Overview

Mini Docker is composed of multiple modular components working together:

```
                           ┌─────────────┐
                           │    CLI      │
                           └─────┬───────┘
                                 │
          ┌──────────────────────┼───────────────────────┐
          │                      │                       │
   ┌──────▼──────┐       ┌───────▼───────┐       ┌───────▼───────┐
   │ Container   │       │  Container    │       │    Network    │
   │ Manager     │       │  Runtime      │       │    Manager    │
   └──────┬──────┘       └───────┬───────┘       └───────────────┘
          │                      │
          │                      ▼
          │            ┌─────────────────┐
          │            │  Linux Kernel   │
          │            │ (namespaces,    │
          │            │  cgroups,       │
          │            │  networking)    │
          │            └─────────────────┘
          │
          ▼
   ┌──────────────┐
   │ Storage      │
   │ Module       │
   └──────────────┘
```

- **CLI** handles user commands and parameter parsing
- **Container Manager** tracks container lifecycles, manages persistent state, and delegates container operations
- **Runtime** launches isolated containers using namespaces and cgroups
- **Network Manager** establishes container networking bridges and virtual interfaces
- **Storage Module** persists container metadata for continuity
- **Linux Kernel** provides core isolation, resource management, and networking capabilities

***

## Architecture

```
+-----------------------------------------------------------+
|                          CLI                              |
+-------------------------------+---------------------------+
                                |
                 +--------------▼--------------+
                 |       Container Manager      |
                 +--------------+--------------+
                                |
        +-----------------------+-----------------------+
        |                       |                       |
+-------▼-------+       +-------▼-------+       +-------▼-------+
|  Runtime      |       | Network       |       | Storage       |
| (namespaces,  |       | Manager       |       | Module        |
|  cgroups)     |       | (bridge, veth)|       | (JSON files)  |
+---------------+       +---------------+       +---------------+
                                |
                         Linux Kernel
                  (namespaces, cgroups, network)
```

***

## Design Decisions

- Async Rust with Tokio for concurrency and process monitoring
- Persistent JSON state storage for durability
- Modular, testable components following SRP and extensibility principles
- Handles WiFi networking gracefully with fallback to host networking
- Command-line experience modeled closely after Docker CLI

***

## Limitations

- Linux only (relies on kernel namespaces and cgroups)
- Basic networking (no advanced network plugins)
- Requires root privileges for system-level operations
- Image management limited to overlayfs layers

***

## Documentation

### Project Structure

- `src/cli/` — CLI commands and argument parsing
- `src/container/` — Container lifecycle management
- `src/runtime/` — Container creation with namespaces and cgroups
- `src/image/` — Image layering and management
- `src/network/` — Container networking with bridge and veth management
- `src/storage/` — Persistent container metadata storage
- `examples/` — Demonstrations such as networking_demo

### Key Concepts

- Linux namespaces for process isolation
- Cgroups for resource limitations
- OverlayFS for layered filesystems
- Veth and network bridge for container networking
- Persistent container state for CLI continuity

### Development & Testing

- Use `cargo run` for development with easy recompile and test
- Use `cargo build --release` for optimized builds
- Networking requires root privileges and setup of utilities
- Run the networking demo example for full feature demonstration

### Known Issues and Workarounds

- Network bridge not supported on WiFi; host networking fallback enabled
- Containers running short commands exit quickly, visible with `ps --all`

***

## Contributing

Contributions, bug reports, and feature requests are welcome! Please fork, improve, and submit pull requests.

***

## License

Licensed under the MIT License - see LICENSE file.

***

## Contact

Project Author: Aarambh Dev Hub
GitHub: [https://github.com/AarambhDevHub](https://github.com/AarambhDevHub)

***

*Built with Rust, inspired by Docker and Linux kernel internals.* 🚀

***

**Made with ❤️ and 🦀 Rust ❤️ by [Aarambh Dev Hub](https://youtube.com/@aarambhdevhub)**
