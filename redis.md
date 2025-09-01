# Mini Redis

A lightweight, Redis-compatible in-memory key-value database built in Rust with TCP server support and persistence.

## 🚀 Features

- **In-Memory Storage**: Fast HashMap-based key-value operations
- **TCP Server**: Async server supporting multiple concurrent connections
- **Redis Protocol**: Compatible with Redis RESP (REdis Serialization Protocol)
- **Persistence**: Append-only logging with automatic crash recovery
- **CLI Client**: Interactive command-line client with REPL interface
- **Thread-Safe**: Concurrent access using Arc<RwLock<>>
- **Error Handling**: Comprehensive error types and graceful degradation
- **Structured Logging**: Built-in observability with tracing

## 📋 Supported Commands

| Command | Description | Example |
|---------|-------------|---------|
| `SET` | Set a key-value pair | `SET mykey "hello world"` |
| `GET` | Get value by key | `GET mykey` |
| `DEL` | Delete a key | `DEL mykey` |
| `PING` | Test server connection | `PING` |
| `INFO` | Get server information | `INFO` |

## 🏗️ Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   TCP Client    │───▶│   TCP Server     │───▶│  Memory Store   │
│                 │    │                  │    │                 │
│ • CLI Interface │    │ • Connection     │    │ • HashMap       │
│ • RESP Protocol │    │   Management     │    │ • Command Exec  │
│ • Command Parse │    │ • Async I/O      │    │ • Concurrency   │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                                         │
                                                         ▼
                                               ┌─────────────────┐
                                               │   Persistence   │
                                               │                 │
                                               │ • Append Log    │
                                               │ • JSON Format   │
                                               │ • Crash Recovery│
                                               └─────────────────┘
```

## 🛠️ Installation

### Prerequisites

- Rust 1.70+
- Cargo

### Build from Source

```bash
# Navigate to the project directory
cd mini-redis

# Build the project
cargo build --release

# Or build in development mode
cargo build
```

## 🚀 Usage

### Starting the Server

```bash
# Run with default settings (127.0.0.1:6379)
cargo run --bin mini-redis-server

# Custom configuration
cargo run --bin mini-redis-server -- --port 8080 --host 0.0.0.0 --data-dir ./data
```

**Server Options:**
- `--port, -p`: Port to listen on (default: 6379)
- `--host`: Host to bind to (default: 127.0.0.1)
- `--data-dir, -d`: Data directory for persistence (default: data)

### Using the CLI Client

```bash
# Connect to default server
cargo run --bin mini-redis-client

# Connect to custom server
cargo run --bin mini-redis-client -- --server 192.168.1.100:8080
```

### Example Session

```bash
mini-redis> SET user:1 "John Doe"
OK

mini-redis> GET user:1
"John Doe"

mini-redis> SET counter 42
OK

mini-redis> GET counter
"42"

mini-redis> DEL user:1
(integer) 1

mini-redis> GET user:1
(nil)

mini-redis> PING
PONG

mini-redis> INFO
# Server
redis_version:mini-redis-0.1.0
# Keyspace
db0:keys=1,expires=0
# Stats
total_operations:4
uptime_seconds:127

mini-redis> quit
Goodbye!
```

### Using with Redis Clients

Mini Redis is compatible with standard Redis clients. Example with `redis-cli`:

```bash
# Install redis-tools if needed
# Ubuntu/Debian: sudo apt-get install redis-tools

redis-cli -h 127.0.0.1 -p 6379
127.0.0.1:6379> SET hello world
OK
127.0.0.1:6379> GET hello
"world"
```

## 📁 Project Structure

```
mini-redis/
├── Cargo.toml              # Project configuration & dependencies
├── README.md               # This file
├── src/
│   ├── main.rs            # Server entry point
│   ├── lib.rs             # Library root & error definitions
│   ├── server/
│   │   ├── mod.rs         # Server module exports
│   │   ├── tcp_server.rs  # TCP server implementation
│   │   └── connection.rs  # Client connection handling
│   ├── storage/
│   │   ├── mod.rs         # Storage module exports
│   │   ├── memory_store.rs # In-memory HashMap storage
│   │   └── persistence.rs  # Append-only log persistence
│   ├── protocol/
│   │   ├── mod.rs         # Protocol module exports
│   │   ├── parser.rs      # RESP protocol parser
│   │   └── command.rs     # Command & response definitions
│   └── client/
│       ├── mod.rs         # Client module exports
│       ├── tcp_client.rs  # TCP client implementation
│       └── main.rs        # CLI client entry point
└── data/                  # Persistence directory (auto-created)
    └── mini-redis.log     # Append-only operation log
```

## 🔧 Development

### Running Tests

```bash
# Run all tests
cargo test

# Run with output
cargo test -- --nocapture

# Test specific module
cargo test storage
```

### Development Build

```bash
# Build in debug mode
cargo build

# Run with debug logging
RUST_LOG=debug cargo run --bin mini-redis-server
```

### Code Formatting & Linting

```bash
# Format code
cargo fmt

# Run clippy linter
cargo clippy -- -D warnings
```

## 🔍 Technical Details

### Persistence Model
- **Append-Only Log**: All write operations (SET, DEL) are logged to disk
- **JSON Format**: Each operation is serialized as JSON for easy debugging
- **Crash Recovery**: On startup, the server replays the log to restore state
- **Write-Through**: Operations are logged synchronously before responding

### Concurrency Model
- **Actor Pattern**: Each client connection runs in its own async task
- **Shared State**: Memory store is shared using `Arc<RwLock<MemoryStore>>`
- **Read-Write Lock**: Multiple concurrent reads, exclusive writes

### Protocol Compatibility
- **RESP Support**: Implements Redis Serialization Protocol v2
- **Array Commands**: Supports `*3\r\n$3\r\nSET\r\n$3\r\nkey\r\n$5\r\nvalue\r\n`
- **Simple Strings**: Supports `SET key value` format
- **Response Types**: OK, Error, Integer, Bulk String, Nil

### Memory Management
- **Zero-Copy**: Efficient string handling where possible
- **Bounded Growth**: Memory usage grows linearly with stored data
- **No Expiration**: Keys persist until explicitly deleted (future enhancement)

## 🚦 Performance Characteristics

- **Throughput**: ~50K ops/sec on modern hardware (single-threaded)
- **Latency**: Sub-millisecond response times for in-memory operations
- **Memory**: ~48 bytes overhead per key-value pair
- **Durability**: Fsync on every write operation (configurable in future)

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## ☕ Support the Project
If you find this project helpful, consider buying me a coffee!
[Buy Me a Coffee](https://buymeacoffee.com/aarambhdevhub)

## 🙏 Acknowledgments

- Inspired by Redis and its elegant design
- Built with the amazing Rust ecosystem
- Thanks to the Tokio team for excellent async runtime

***

**Made with ❤️ and 🦀 Rust ❤️ by [Aarambh Dev Hub](https://youtube.com/@aarambhdevhub)**

*Mini Redis demonstrates that systems programming languages can be both safe AND fast for database workloads.*
