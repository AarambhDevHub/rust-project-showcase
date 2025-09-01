# Mini Search Engine in Rust

A full-featured mini search engine implemented in Rust with support for crawling web content, building inverted indexes, TF-IDF ranking, and powerful search APIs.

## 🚀 Features

- **🌐 Web Crawling** - Polite web crawling with domain restrictions and configurable delays
- **📁 Local File Indexing** - Index text files and markdown documents from directories
- **🔍 Inverted Index** - Efficient term-to-document mappings with posting lists
- **📊 TF-IDF Ranking** - Industry-standard relevance scoring algorithm
- **💻 CLI Interface** - Command-line tools for indexing and searching
- **🌍 HTTP REST API** - RESTful web service for programmatic access
- **✨ Beautiful Web Frontend** - Modern, responsive search interface
- **💾 Persistent Storage** - Sled embedded database with JSON export option
- **⚡ Async Support** - High-performance concurrent operations with Tokio

## 🏗 System Architecture

```
                           Mini Search Engine Architecture
                           ═══════════════════════════════

    ┌─────────────────┐         ┌─────────────────┐         ┌─────────────────┐
    │   Web Content   │         │  Local Files    │         │   User Input    │
    │                 │         │                 │         │                 │
    │ -  HTML Pages    │         │ -  Text Files    │         │ -  Search Query  │
    │ -  Articles      │         │ -  Markdown      │         │ -  Index Cmds    │
    │ -  Documentation │         │ -  Documents     │         │                 │
    └─────────┬───────┘         └─────────┬───────┘         └─────────┬───────┘
              │                           │                           │
              ▼                           ▼                           ▼
    ┌─────────────────┐         ┌─────────────────┐         ┌─────────────────┐
    │  Web Crawler    │         │  File Crawler   │         │   User APIs     │
    │                 │         │                 │         │                 │
    │ -  HTTP Requests │         │ -  Directory     │         │ -  CLI Interface │
    │ -  Link Discovery│         │   Traversal     │         │ -  HTTP Server   │
    │ -  Content Parse │         │ -  File Reading  │         │ -  Web Frontend  │
    └─────────┬───────┘         └─────────┬───────┘         └─────────┬───────┘
              │                           │                           │
              └─────────┬─────────────────┘                           │
                        │                                             │
                        ▼                                             │
              ┌─────────────────┐                                     │
              │   Document      │◄────────────────────────────────────┘
              │   Processing    │
              │                 │
              │ -  Text Extract  │
              │ -  Metadata      │
              │ -  Tokenization  │
              └─────────┬───────┘
                        │
                        ▼
              ┌─────────────────┐
              │   Tokenizer     │
              │                 │
              │ -  Word Split    │
              │ -  Normalize     │
              │ -  Stop Words    │
              │ -  Filter Short  │
              └─────────┬───────┘
                        │
                        ▼
              ┌─────────────────┐
              │ Inverted Index  │
              │                 │
              │ -  Term -> Docs  │
              │ -  Frequencies   │
              │ -  Posting Lists │
              │ -  Hash Maps     │
              └─────────┬───────┘
                        │
                        ▼
              ┌─────────────────┐         ┌─────────────────┐
              │ Storage Layer   │◄────────┤   TF-IDF        │
              │                 │         │   Ranker        │
              │ -  Sled Database │         │                 │
              │ -  JSON Files    │         │ -  Score Calc    │
              │ -  Persistence   │         │ -  Relevance     │
              │ -  Serialization │         │ -  Result Sort   │
              └─────────┬───────┘         └─────────────────┘
                        │                           ▲
                        │                           │
                        ▼                           │
              ┌─────────────────┐                   │
              │ Search Engine   │───────────────────┘
              │                 │
              │ -  Query Process │
              │ -  Index Search  │
              │ -  Result Merge  │
              │ -  Async Ops     │
              └─────────┬───────┘
                        │
                        ▼
              ┌─────────────────┐
              │ Search Results  │
              │                 │
              │ -  Ranked Docs   │
              │ -  Scores        │
              │ -  Snippets      │
              │ -  Metadata      │
              └─────────────────┘

     Data Flow: Content → Crawling → Processing → Indexing → Storage → Search → Results
```

### Architecture Components

#### **Input Layer**
- **Web Content**: HTML pages, articles, documentation from websites
- **Local Files**: Text files, markdown documents from directories
- **User Input**: Search queries and indexing commands via CLI/Web

#### **Crawling Layer**
- **Web Crawler**: Fetches web content with HTTP requests, discovers links, parses HTML
- **File Crawler**: Traverses directories, reads local files, extracts content

#### **Processing Layer**
- **Document Processing**: Extracts text, handles metadata, prepares for tokenization
- **Tokenizer**: Splits text into words, normalizes case, removes stop words, filters short terms

#### **Index Layer**
- **Inverted Index**: Maps terms to documents, tracks frequencies, maintains posting lists using hash maps
- **Storage Layer**: Persists index using Sled database or JSON files with serialization

#### **Search Layer**
- **TF-IDF Ranker**: Calculates relevance scores, ranks results by importance
- **Search Engine**: Processes queries, searches index, merges results, handles async operations

#### **Output Layer**
- **Search Results**: Returns ranked documents with scores, snippets, and metadata

### Key Design Principles

- **Async Processing** - Non-blocking I/O with Tokio runtime
- **Type Safety** - Rust's ownership system prevents memory bugs
- **Modularity** - Clean interfaces between all components
- **Performance** - Efficient data structures and zero-cost abstractions
- **Scalability** - Supports both small and large document collections

## 📁 Project Structure

```
mini-search-engine/
├── Cargo.toml
├── README.md
├── src/
│   ├── main.rs           # Main entry point
│   ├── lib.rs           # Library exports
│   ├── core/            # Core search engine logic
│   │   ├── mod.rs
│   │   ├── document.rs   # Document data structures
│   │   ├── index.rs     # Inverted index implementation
│   │   ├── tokenizer.rs # Text tokenization
│   │   └── ranking.rs   # TF-IDF ranking algorithm
│   ├── crawler/         # Content crawling
│   │   ├── mod.rs
│   │   ├── file_crawler.rs
│   │   └── web_crawler.rs
│   ├── storage/         # Persistence layer
│   │   ├── mod.rs
│   │   ├── json_storage.rs
│   │   └── sled_storage.rs
│   ├── search/          # Search engine orchestration
│   │   ├── mod.rs
│   │   └── engine.rs
│   ├── api/             # User interfaces
│   │   ├── mod.rs
│   │   ├── cli.rs       # Command-line interface
│   │   └── http.rs      # HTTP server
│   └── web/             # Web frontend
│       ├── mod.rs
│       └── template.rs  # HTML template
├── data/                # Generated data
│   ├── documents/       # Sample documents
│   └── index/           # Search index storage
└── examples/            # Usage examples
    ├── sample_docs/
    └── usage.rs
```

## 🛠 Getting Started

### Prerequisites

- **Rust** 1.70+ ([Install Rust](https://rustup.rs/))
- **Cargo** (included with Rust)

### Installation

```
cd mini-search-engine
cargo build --release
```

### Quick Start

1. **Index some content:**
```
# Index a website
cargo run -- index-site --url "https://rust-lang.org" --max-pages 5

# Index local files
cargo run -- index --directory examples/sample_docs
```

2. **Search via CLI:**
```
cargo run -- search --query "rust programming" --limit 10
```

3. **Start web server:**
```
cargo run server 3030
```

4. **Open web interface:** [http://localhost:3030](http://localhost:3030)

## 💻 CLI Usage

### Commands

| Command | Description |
|---------|-------------|
| `index --directory <path>` | Index local files |
| `index-site --url <url> --max-pages <n>` | Index website |
| `index-web --urls <url1,url2> --max-pages <n>` | Index specific URLs |
| `search --query <terms> --limit <n>` | Search documents |
| `stats` | Show index statistics |
| `clear` | Clear search index |

### Examples

```
# Index local documentation
cargo run -- index --directory ./docs

# Index Rust documentation
cargo run -- index-site --url "https://doc.rust-lang.org" --max-pages 100

# Search with custom limit
cargo run -- search --query "memory safety ownership" --limit 20

# View index statistics
cargo run -- stats
```

## 🌍 HTTP API

Start the web server:
```
cargo run server 3030
```

### Endpoints

| Method | Endpoint | Description | Example |
|--------|----------|-------------|---------|
| `GET` | `/` | Web interface | Browser access |
| `GET` | `/search` | Search documents | `?q=rust&limit=10` |
| `GET` | `/stats` | Index statistics | JSON response |
| `GET` | `/status` | Health check | Server status |
| `POST` | `/index` | Index directory | `{"directory": "/path"}` |
| `POST` | `/index-web` | Index URLs | `{"urls": ["url1"], "max_pages": 50}` |
| `POST` | `/index-site` | Index website | `{"url": "https://site.com", "max_pages": 100}` |

### API Examples

```
# Search via API
curl "http://localhost:3030/search?q=rust%20programming&limit=5"

# Get statistics
curl "http://localhost:3030/stats"

# Index a website
curl -X POST http://localhost:3030/index-site \
  -H "Content-Type: application/json" \
  -d '{"url": "https://rust-lang.org", "max_pages": 10}'
```

### Response Format

```
{
  "query": "rust programming",
  "results": [
    {
      "title": "Rust Programming Language",
      "path": "https://rust-lang.org/",
      "score": 5.9120,
      "snippet": "Rust is a systems programming language..."
    }
  ],
  "total": 1
}
```

## 🎨 Web Interface Features

- **🔍 Real-time search** with instant results
- **📱 Responsive design** for mobile and desktop
- **⭐ Relevance scores** visible for each result
- **📄 Content snippets** with search term context
- **🎯 Example queries** for quick testing
- **⚡ Fast, modern UI** with smooth animations

## 🎯 Key Algorithms

### TF-IDF Scoring

```
score(term, doc) = tf(term, doc) × idf(term)

where:
- tf(term, doc) = 1 + log(frequency)
- idf(term) = log(total_docs / docs_containing_term)
```

### Text Processing

1. **Tokenization** - Split text into words
2. **Normalization** - Convert to lowercase
3. **Stop word removal** - Filter common words
4. **Term filtering** - Remove short terms (<3 chars)

## 📊 Performance

- **Indexing speed** - ~1000 documents/second
- **Search latency** - Sub-100ms for most queries
- **Memory usage** - Efficient with Rust's zero-cost abstractions
- **Concurrent operations** - Multi-threaded with Tokio

## 🔧 Configuration

### Environment Variables

```
RUST_LOG=debug    # Enable debug logging
DATABASE_PATH=./custom/path    # Custom database location
```

### Crawler Settings

- **Delay between requests** - 1-2 seconds (configurable)
- **Maximum pages per site** - Configurable limit
- **Domain restrictions** - Whitelist/blacklist support
- **Timeout handling** - 30-second request timeout

## 🧪 Testing

```
# Run all tests
cargo test

# Run with output
cargo test -- --nocapture

# Test specific module
cargo test core::index

# Run example
cargo run --example usage
```

## 🚀 Deployment

### Production Build

```
cargo build --release
./target/release/search-cli server 8080
```

### Docker Support

```
FROM rust:1.70 as builder
WORKDIR /app
COPY . .
RUN cargo build --release

FROM debian:bookworm-slim
COPY --from=builder /app/target/release/search-cli /usr/local/bin/
EXPOSE 3030
CMD ["search-cli", "server", "3030"]
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## ☕ Support the Project
If you find this project helpful, consider buying me a coffee!
[Buy Me a Coffee](https://buymeacoffee.com/aarambhdevhub)

## 🙏 Acknowledgments

- **Rust Community** - For the amazing ecosystem
- **Information Retrieval** - Classic IR algorithms and concepts
- **Modern Search Engines** - Inspiration from Elasticsearch, Solr
- **Web Standards** - HTML5, CSS3, and modern JavaScript

---

**Built with ❤️ and Rust** 🦀**❤️ by [Aarambh Dev Hub](https://youtube.com/@aarambhdevhub)**
