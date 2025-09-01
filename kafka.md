# Mini Kafka - Distributed Message Queue & Pub/Sub System

A lightweight Kafka-inspired distributed message queue and pub/sub system built in Rust with async concurrency, persistent storage, and distributed logging. Perfect for learning distributed systems concepts or as a lightweight alternative to Apache Kafka for smaller applications.


![Performance](https://img.shields.io/badge/consumptionust and async/await for maximum throughput and low latency
- **Message Queuing**: Reliable message delivery with offset tracking and persistence
- **Pub/Sub System**: Subscribe to topics and receive real-time message notifications
- **Partitioning**: Distribute messages across multiple partitions for horizontal scalability
- **Persistent Storage**: Messages are durably stored on disk with configurable retention
- **Consumer Groups**: Support for multiple consumer groups with automatic offset management
- **Auto Topic Creation**: Topics are automatically created when first accessed
- **Offset Management**: Persistent consumer offset tracking with auto-commit support
- **Distributed Logging**: Built-in structured logging with search capabilities
- **TCP Protocol**: Custom binary protocol for efficient client-broker communication
- **Batch Operations**: Send multiple messages efficiently with batch APIs
- **Storage Management**: Advanced storage analysis and performance monitoring
- **Timeout Handling**: Robust timeout management for reliable operations
- **Error Handling**: Comprehensive error types and graceful failure handling
- **Sub-microsecond reads**: 347 ns consumption time
- **Fast memory operations**: 673 ns message creation
- **Efficient serialization**: 4.1 µs encoding/decoding
- **Reliable throughput**: 24-453 KiB/s depending on message size

## 📦 Installation

### Prerequisites

- Rust 1.70 or higher
- Tokio runtime for async operations

### Run Tests

```bash
# Unit tests
cargo test

# Integration tests
cargo test --test integration_tests

# All tests with output
RUST_LOG=debug cargo test -- --nocapture
```

## 🏃 Quick Start

### 1. Start the Broker

```bash
# Default configuration (localhost:9092)
cargo run

# Custom configuration
cargo run -- --broker-id 1 --address 127.0.0.1:9092

# With environment variables
RUST_LOG=info cargo run
```

### 2. Send Messages (Producer)

```bash
# Run the simple producer example
cargo run --example simple_producer
```

Or create your own producer:

```rust
use mini_kafka::Producer;

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    let producer = Producer::new(
        "127.0.0.1:9092".to_string(),
        "my-producer".to_string()
    );

    // Send a message
    let offset = producer.send(
        "my-topic".to_string(),
        Some("key1".to_string()),
        "Hello, Mini Kafka!".as_bytes().to_vec()
    ).await?;

    println!("Message sent at offset: {}", offset);
    Ok(())
}
```

### 3. Consume Messages

```bash
# Run the simple consumer example
cargo run --example simple_consumer
```

Or create your own consumer:

```rust
use mini_kafka::Consumer;
use tokio::time::Duration;

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    let mut consumer = Consumer::new(
        "127.0.0.1:9092".to_string(),
        "my-group".to_string(),
        "my-consumer".to_string()
    )
    .with_auto_commit(true)
    .with_commit_interval(Duration::from_secs(5));

    // Subscribe to topic
    consumer.subscribe("my-topic".to_string(), vec![0, 1, 2]).await?;

    // Start consuming
    consumer.start_consuming(|message| {
        println!("Received: {}", String::from_utf8_lossy(&message.payload));
        true // Continue consuming
    }).await?;

    Ok(())
}
```

## 📖 Examples

The project includes comprehensive examples demonstrating various features:

### Basic Examples

```bash
# Simple producer/consumer pair
cargo run --example simple_producer
cargo run --example simple_consumer

# Pub/Sub demonstration with offset management
cargo run --example pubsub_demo
```

### Advanced Examples

```bash
# Distributed setup with multiple brokers
cargo run --example distributed_setup

# Storage management and performance analysis
cargo run --example storage_management_demo
```

### Example Output

```
🚀 Starting Pub/Sub Demo with Offset Management
===============================================
🔔 Consumer subscribed successfully and resumed from stored offsets
👂 Consumer waiting for messages...

📤 Starting producer...
📤 Publishing: 🎉 Welcome to Mini Kafka Pub/Sub!
   ✓ Published at offset 0
📨 Notification #1: 🎉 Welcome to Mini Kafka Pub/Sub! (partition: 1, key: Some("notif-0"))

📤 Publishing: 📢 System maintenance scheduled for tonight
   ✓ Published at offset 1
📨 Notification #2: 📢 System maintenance scheduled for tonight (partition: 0, key: Some("notif-1"))

✅ Consumer finished processing 7 notifications
🎉 Pub/Sub demo completed successfully!
   - Messages were published with keys for partitioning
   - Consumer automatically resumed from stored offsets
   - Offsets were automatically committed every 2 seconds
```

## ⚙️ Configuration

### Default Configuration

```rust
pub struct Config {
    pub broker: BrokerConfig {
        id: 1,
        data_dir: "data/broker".to_string(),
        default_partitions: 3,
        retention_ms: 604800000, // 7 days
        max_message_size: 1048576, // 1MB
    },
    pub network: NetworkConfig {
        bind_address: "127.0.0.1:9092".parse().unwrap(),
        max_connections: 100,
        request_timeout_ms: 30000,
    },
    pub logging: LoggingConfig {
        level: "info".to_string(),
        log_dir: "data/logs".to_string(),
        max_log_size: 104857600, // 100MB
        retention_days: 7,
    },
}
```

### Command Line Options

```bash
USAGE:
    mini-kafka [OPTIONS]

OPTIONS:
    -c, --config <CONFIG>        Configuration file path [default: config.json]
    -b, --broker-id <BROKER_ID>  Broker ID [default: 1]
    -a, --address <ADDRESS>      Bind address [default: 127.0.0.1:9092]
    -h, --help                   Print help information
    -V, --version                Print version information
```

## 📊 Performance Benchmarks

Here are the latest performance benchmarks for Mini Kafka (generated on August 30, 2025):

### Production Performance
| Benchmark | Time Range | Throughput |
|-----------|------------|------------|
| **Small Messages (64B)** | 2.03 - 2.54 ms | 24.6 - 30.8 KiB/s |
| **Large Messages (4KB)** | 8.82 - 12.5 ms | 320 - 453 KiB/s |

### Consumption Performance
| Benchmark | Time |
|-----------|------|
| **Sequential Reads** | ~347 ns |

### Memory Operations
| Benchmark | Time |
|-----------|------|
| **Message Creation** | ~673 ns |
| **Serialization/Deserialization** | ~4.1 µs |

### Performance Summary
- ✅ **Excellent**: Sub-microsecond consumption (347 ns)
- ✅ **Very Good**: Fast message creation (673 ns)
- ✅ **Good**: Efficient serialization (4.1 µs)
- ⚠️ **Optimization Opportunity**: Message production (2-12 ms)

*Benchmarks run with Criterion.rs on GitHub Actions (Ubuntu latest)*

## 🏗️ Architecture

### System Overview

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│    Producer     │    │    Consumer     │    │  Storage Mgmt   │
│                 │    │   (Groups)      │    │     Demo        │
└─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘
          │                      │                      │
          │        TCP           │          TCP         │
          │     Protocol         │       Protocol       │
          │                      │                      │
          └──────────────────────┼──────────────────────┘
                                 │
                     ┌───────────▼───────────┐
                     │                       │
                     │    Mini Kafka         │
                     │       Broker          │
                     │                       │
                     │  ┌─────────────────┐  │
                     │  │     Topics      │  │
                     │  │   ┌───────────┐ │  │
                     │  │   │Partition 0│ │  │
                     │  │   │Partition 1│ │  │
                     │  │   │Partition 2│ │  │
                     │  │   └───────────┘ │  │
                     │  └─────────────────┘  │
                     │                       │
                     │  ┌─────────────────┐  │
                     │  │ Offset Storage  │  │
                     │  │ Message Storage │  │
                     │  │   (Disk-based)  │  │
                     │  └─────────────────┘  │
                     └───────────────────────┘
```

### Core Components

- **Broker**: Central message coordinator managing topics, partitions, and storage
- **Producer**: Sends messages to topics with automatic partitioning and batching
- **Consumer**: Subscribes to topics with consumer group support and offset management
- **Storage**: Persistent disk-based storage for messages and consumer offsets
- **Network**: Efficient TCP-based binary protocol for client-broker communication
- **Logging**: Distributed logging system with structured logs and search capabilities

### Message Flow

1. **Producer** connects to broker and sends messages with optional keys
2. **Broker** assigns messages to partitions using key-based or round-robin distribution
3. **Messages** are persisted to disk with automatic durability guarantees
4. **Consumer** polls broker for new messages from subscribed topics and partitions
5. **Offsets** are automatically tracked and committed per consumer group
6. **Storage** provides persistence for both messages and consumer state

## 📚 API Reference

### Producer API

```rust
impl Producer {
    // Create a new producer with client identification
    pub fn new(broker_address: String, client_id: String) -> Self

    // Send a single message with optional key for partitioning
    pub async fn send(&self, topic: String, key: Option<String>, payload: Vec<u8>) -> KafkaResult<u64>

    // Send multiple messages efficiently in a batch
    pub async fn send_batch(&self, messages: Vec<(String, Option<String>, Vec<u8>)>) -> KafkaResult<Vec<u64>>

    // Get producer client information
    pub fn client_info(&self) -> (&str, &str)
}
```

### Consumer API

```rust
impl Consumer {
    // Create a new consumer with group and client identification
    pub fn new(broker_address: String, group_id: String, client_id: String) -> Self

    // Configure auto-commit behavior
    pub fn with_auto_commit(self, auto_commit: bool) -> Self
    pub fn with_commit_interval(self, interval: Duration) -> Self

    // Subscribe to topics with specific partitions
    pub async fn subscribe(&mut self, topic: String, partitions: Vec<u32>) -> KafkaResult<()>

    // Consume a single message
    pub async fn consume_one(&mut self) -> KafkaResult<Option<Message>>

    // Start consuming with callback function
    pub async fn start_consuming<F>(&mut self, callback: F) -> KafkaResult<()>
    where F: FnMut(Message) -> bool + Send

    // Manually commit offsets
    pub async fn commit_offsets(&mut self) -> KafkaResult<()>

    // Get consumer information
    pub fn client_info(&self) -> (&str, &str, &str)
}
```

### Message Structure

```rust
#[derive(Debug, Clone, Serialize, Deserialize, PartialEq)]
pub struct Message {
    pub id: Uuid,                                    // Unique message ID
    pub topic: String,                               // Topic name
    pub partition: u32,                              // Partition number
    pub key: Option<String>,                         // Optional message key
    pub payload: Vec<u8>,                           // Message payload
    pub timestamp: chrono::DateTime<chrono::Utc>,   // Creation timestamp
    pub headers: HashMap<String, String>,           // Custom headers
}

impl Message {
    pub fn new(topic: String, partition: u32, payload: Vec<u8>) -> Self
    pub fn with_key(self, key: String) -> Self
    pub fn with_header(self, key: String, value: String) -> Self
}
```

## 🧪 Testing

### Unit Tests

```bash
# Run all unit tests
cargo test

# Run specific test module
cargo test --lib broker::tests

# Run with output
cargo test -- --nocapture
```

### Integration Tests

```bash
# Run integration tests
cargo test --test integration_tests

# Run specific integration test
cargo test --test integration_tests test_produce_and_consume
```

### Performance Testing

The project includes performance analysis through the storage management demo:

```bash
# Analyze storage performance with different message sizes
cargo run --example storage_management_demo
```

Example performance output:
```
📊 Storage Analysis for 'storage-heavy':
   - Messages processed: 47
   - Total bytes consumed: 48,158 bytes
   - Average message size: 1024.6 bytes
   - Processing time: 26.82 seconds
   - Throughput: 1.8 messages/sec
   - Message types: {"heavy": 46}

📊 Storage Analysis for 'storage-light':
   - Messages processed: 39
   - Total bytes consumed: 1,628 bytes
   - Average message size: 41.7 bytes
   - Processing time: 26.77 seconds
   - Throughput: 1.5 messages/sec
   - Message types: {"light": 38}
```

## 📊 Monitoring & Logging

### Structured Logging

Mini Kafka uses structured logging with multiple levels:

```bash
# Debug level (verbose)
RUST_LOG=debug cargo run

# Info level (default)
RUST_LOG=info cargo run

# Warn level (warnings only)
RUST_LOG=warn cargo run

# Error level (errors only)
RUST_LOG=error cargo run
```

### Built-in Metrics

The system provides comprehensive metrics through examples:

- **Message throughput** (messages/second)
- **Storage efficiency** (bytes per message, total storage usage)
- **Consumer lag** (offset tracking per consumer group)
- **Partition distribution** (message distribution across partitions)
- **Connection handling** (client connection management)
- **Error rates** (comprehensive error tracking)

### Storage Analysis

The storage management demo provides detailed storage insights:

```bash
🗂️ Storage File Analysis:
   📁 Storage location: data/storage_demo
   - Total partition files: 6
   - Total storage size: 102,682 bytes (100.3 KB)
```

## 🚀 Production Considerations

### Deployment Features

- **Persistent Storage**: All messages and offsets are stored durably on disk
- **Consumer Groups**: Multiple consumer instances with automatic load balancing
- **Offset Management**: Reliable offset tracking with auto-commit capabilities
- **Error Recovery**: Comprehensive error handling and graceful degradation
- **Performance Monitoring**: Built-in performance analysis tools
- **Timeout Management**: Robust timeout handling for reliable operations

### Configuration Recommendations

For production use, consider these settings:

1. **Increase partition count** for higher throughput: `default_partitions: 6`
2. **Tune retention policies**: `retention_ms: 259200000` (3 days)
3. **Optimize commit intervals**: `commit_interval: Duration::from_secs(10)`
4. **Configure appropriate timeouts**: `request_timeout_ms: 30000`
5. **Set memory limits**: `max_message_size: 10485760` (10MB)
6. **Use dedicated storage**: Fast SSD storage for data directory

## 🔧 Development

### Project Structure

```
mini-kafka/
├── src/
│   ├── broker/          # Core broker implementation
│   │   ├── mod.rs       # Broker coordination
│   │   ├── partition.rs # Partition management
│   │   ├── topic.rs     # Topic handling
│   │   └── storage.rs   # Persistent storage
│   ├── client/          # Client implementations
│   │   ├── producer.rs  # Producer client
│   │   └── consumer.rs  # Consumer client
│   ├── network/         # Network protocol
│   │   ├── server.rs    # TCP server
│   │   └── protocol.rs  # Message protocol
│   ├── logging/         # Distributed logging
│   │   └── distributed_log.rs
│   └── config/          # Configuration management
│       └── settings.rs
├── examples/            # Comprehensive examples
│   ├── simple_producer.rs
│   ├── simple_consumer.rs
│   ├── pubsub_demo.rs
│   ├── distributed_setup.rs
│   └── storage_management_demo.rs
└── tests/              # Test suite
    ├── integration_tests.rs
    └── unit_tests.rs
```

### Key Features Implemented

- ✅ **Message Persistence**: Durable storage with bincode serialization
- ✅ **Consumer Groups**: Offset tracking per group with auto-commit
- ✅ **Partition Management**: Key-based and round-robin message distribution
- ✅ **Network Protocol**: Efficient TCP-based binary communication
- ✅ **Error Handling**: Comprehensive error types and recovery
- ✅ **Async Operations**: Full async/await support with Tokio
- ✅ **Batch Operations**: Efficient batch message sending
- ✅ **Auto Topic Creation**: Automatic topic creation on first use
- ✅ **Timeout Management**: Robust timeout handling in examples
- ✅ **Storage Analysis**: Performance monitoring and analysis tools

## 🤝 Contributing

We welcome contributions! Here's how to get started:

### Development Setup

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Make your changes and add tests
4. Ensure tests pass: `cargo test`
5. Run examples: `cargo run --example simple_producer`
6. Commit your changes: `git commit -m 'Add amazing feature'`
7. Push to the branch: `git push origin feature/amazing-feature`
8. Open a pull request

### Code Style

- Follow Rust standard formatting: `cargo fmt`
- Run clippy for linting: `cargo clippy`
- Add documentation for public APIs
- Include unit tests for new functionality
- Add examples for new features

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **Apache Kafka** - Inspiration for the design and concepts
- **Tokio** - Async runtime for Rust providing excellent performance
- **Rust Community** - Amazing ecosystem and development tools
- **Contributors** - Everyone who has helped improve this project

## 📞 Support & Community

- **Issues**: [GitHub Issues](https://github.com/yourusername/mini-kafka/issues)
- **Discussions**: [GitHub Discussions](https://github.com/yourusername/mini-kafka/discussions)
- **Documentation**: Run examples to see the system in action
- **Performance**: Use `storage_management_demo` for performance analysis

## 🗺️ Roadmap

### Completed ✅
- Core message queue functionality
- Producer and consumer clients
- Persistent storage with offset management
- Consumer groups with auto-commit
- Comprehensive examples and documentation
- Storage performance analysis
- Timeout and error handling

***

**Mini Kafka** provides a solid foundation for understanding distributed messaging systems while being practical enough for real-world use in smaller applications. The comprehensive examples and storage analysis tools make it an excellent learning platform for distributed systems concepts.

***

**Made with ❤️ and 🦀 Rust ❤️ by [Aarambh Dev Hub](https://youtube.com/@aarambhdevhub)**
