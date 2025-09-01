# Mini Git - Local Git Implementation in Rust 🦀

A complete, educational implementation of Git version control system written in Rust. Mini Git demonstrates Git's core concepts and internal workings through **local repository operations**, making it perfect for understanding how Git really works under the hood.

## 🎯 Educational Focus: Local-Only Implementation

**Mini Git is intentionally designed for local operations only.** This design choice allows you to:
- 🧠 **Learn Git internals** without network protocol complexity
- 🔍 **See exactly how Git works** with object stores, trees, and commits
- 🏗️ **Understand distributed concepts** through local repository simulation
- 📚 **Master Git fundamentals** before tackling network implementations

## ✅ What Mini Git Does (Fully Functional)

### Complete Local Git Experience
- **Repository Management**: `init`, `clone` (local paths)
- **Version Control**: `add`, `commit`, `status`, `log`, `diff`
- **Branching & Merging**: `branch`, `checkout`, `merge` with conflict detection
- **Local Remotes**: `push`/`pull` between local repositories
- **Stashing**: `stash push/pop/list/show/drop/clear`
- **Remote Management**: Add/manage local repository references

### Git-Compatible Object Storage
- SHA-1 content addressing
- Zlib-compressed objects
- Tree and blob management
- Complete commit history

## ❌ What Mini Git Doesn't Do

### Network Operations Not Implemented
- ❌ **GitHub/GitLab**: `https://github.com/user/repo.git`
- ❌ **SSH Remotes**: `git@github.com:user/repo.git`
- ❌ **HTTP/HTTPS**: Any network-based remote URLs
- ❌ **Git Protocols**: `git://` protocol support

**For network operations, use standard Git alongside Mini Git.**

## 🚀 Quick Start

### Installation
```bash
cd mini_git
cargo build --release
```

### Basic Usage
```bash
# Initialize a repository
./target/release/mini_git init

# Add and commit files
echo "Hello, Mini Git!" > hello.txt
./target/release/mini_git add .
./target/release/mini_git commit -m "First commit" --author "You <you@example.com>"

# View history
./target/release/mini_git log
./target/release/mini_git status
```

## 🏗️ Architecture & Design

### System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                          Mini Git Architecture                      │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   User Interface │    │   Commands      │    │   Core Engine   │
│                 │    │                 │    │                 │
│  ┌─────────────┐│    │  ┌─────────────┐│    │  ┌─────────────┐│
│  │    CLI      ││────┤  │    init     ││    │  │ Object Store││
│  │   (clap)    ││    │  │    add      ││    │  │   (SHA-1)   ││
│  └─────────────┘│    │  │   commit    ││    │  └─────────────┘│
│                 │    │  │   status    ││    │                 │
│  ┌─────────────┐│    │  │    log      ││    │  ┌─────────────┐│
│  │ Subcommands ││    │  │   branch    ││────┤  │ Repository  ││
│  │   Parser    ││    │  │  checkout   ││    │  │   Utils     ││
│  └─────────────┘│    │  │   merge     ││    │  └─────────────┘│
└─────────────────┘    │  │   clone     ││    │                 │
                       │  │   push      ││    │  ┌─────────────┐│
                       │  │   pull      ││    │  │   Index     ││
                       │  │  remote     ││    │  │ Management  ││
                       │  │   stash     ││    │  └─────────────┘│
                       │  │   diff      ││    └─────────────────┘
                       │  └─────────────┘│
                       └─────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│                        Data Flow Architecture                       │
└─────────────────────────────────────────────────────────────────────┘

Working Directory     Index (Staging)      Object Database      References
─────────────────     ───────────────      ───────────────      ──────────

┌─────────────┐       ┌─────────────┐      ┌─────────────┐      ┌─────────┐
│   file1.txt │       │  Staged     │      │   Objects   │      │  HEAD   │
│   file2.py  │ ────► │  Changes    │ ───► │             │ ◄──► │         │
│   README.md │  add  │             │commit│ ┌─────────┐ │      │ refs/   │
│     ...     │       │ JSON Index  │      │ │  Blob   │ │      │ heads/  │
└─────────────┘       │   Format    │      │ │  Tree   │ │      │  main   │
                      └─────────────┘      │ │ Commit  │ │      │ feature │
                                           │ └─────────┘ │      └─────────┘
                      ┌─────────────┐      │             │
                      │  Stash      │      │ Compressed  │      ┌─────────┐
                      │  Storage    │ ───► │ (zlib)      │      │ Remote  │
                      │             │      │ SHA-1 Hash  │      │ Tracking│
                      └─────────────┘      └─────────────┘      └─────────┘
```

### Object Store Structure
```
.mini_git/
├── objects/                    # Git Object Database
│   ├── 12/                    # Directory: First 2 chars of SHA-1
│   │   └── 3456789abcdef...   # File: Remaining 38 chars (zlib compressed)
│   ├── ab/
│   │   ├── cdef1234567...     # Blob object (file content)
│   │   └── 9876543210a...     # Tree object (directory structure)
│   └── de/
│       └── f123456789b...     # Commit object (snapshot + metadata)
│
├── refs/                      # Reference Storage
│   ├── heads/                 # Local branch pointers
│   │   ├── main              # Points to commit SHA-1
│   │   └── feature           # Points to commit SHA-1
│   └── remotes/              # Remote tracking branches
│       └── origin/           # Remote named 'origin'
│           ├── main          # Tracks remote main branch
│           └── feature       # Tracks remote feature branch
│
├── index                     # Staging Area (JSON format)
├── HEAD                      # Current branch pointer
├── config                    # Repository configuration
└── stash                     # Stashed changes (JSON array)
```

### Data Model Relationships

```
Commit Object                 Tree Object                 Blob Object
─────────────                ─────────────               ─────────────

┌─────────────────┐          ┌─────────────────┐         ┌─────────────┐
│ hash: abc123... │          │ hash: def456... │         │ hash: 789xyz│
│ parent: xyz789..│ ────────►│ entries: {      │ ──────► │ content:    │
│ tree: def456... │          │   "file.txt": { │         │ "Hello\n"   │
│ author: "Name"  │          │     mode: "644" │         │             │
│ message: "Fix"  │          │     hash: 789xyz│         │             │
│ timestamp: ...  │          │     is_file: T  │         │             │
└─────────────────┘          │   },            │         └─────────────┘
        │                    │   "src/": {     │
        │                    │     mode: "040" │         ┌─────────────┐
        │                    │     hash: sub123│────────►│ hash: sub123│
        └───► Parent         │     is_file: F  │         │ entries: {  │
              Commit         │   }             │         │   "main.rs" │
                            │ }               │         │   ...       │
                            └─────────────────┘         └─────────────┘
                                     │
                            ┌─────────────────┐
                            │ Subdirectory    │
                            │ Tree Object     │
                            └─────────────────┘
```

### Command Execution Flow

```
User Input → CLI Parser → Command Router → Core Operations → Storage

┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐
│ mini_git    │   │    Clap     │   │  Command    │   │  Object     │
│ add file.txt│──►│   Parser    │──►│  Executor   │──►│  Store      │
└─────────────┘   └─────────────┘   └─────────────┘   └─────────────┘
                                           │
Example Flow:                              ▼
                  ┌─────────────────────────────────────────────────────┐
1. add file.txt   │                Command Processing                  │
   │              └─────────────────────────────────────────────────────┘
   ▼                                      │
2. Parse args     ┌─────────────┐         ▼         ┌─────────────┐
   │              │ Read File   │    ┌─────────────┐ │ Update      │
   ▼              │ Content     │───►│ Calculate   │►│ Index       │
3. Load file      └─────────────┘    │ SHA-1 Hash  │ │ (Staging)   │
   │                                 └─────────────┘ └─────────────┘
   ▼                                        │
4. Hash content   ┌─────────────┐           ▼         ┌─────────────┐
   │              │ Compress    │    ┌─────────────┐  │ Success     │
   ▼              │ with zlib   │───►│ Store Blob  │─►│ Message     │
5. Store object   └─────────────┘    │ Object      │  └─────────────┘
   │                                 └─────────────┘
   ▼
6. Update index
```

## 📖 Complete Local Workflows

### 1. Basic Development
```bash
# Start a project
mkdir my_project && cd my_project
mini_git init

# Create files and track changes
echo "# My Project" > README.md
echo "fn main() { println!(\"Hello!\"); }" > main.rs
mini_git add .
mini_git commit -m "Initial commit"

# Check project state
mini_git status
mini_git log
```

### 2. Feature Branch Development
```bash
# Create feature branch
mini_git branch feature-auth
mini_git checkout feature-auth

# Develop feature
echo "Authentication module" > auth.rs
mini_git add auth.rs
mini_git commit -m "Add authentication"

# Merge back to main
mini_git checkout main
mini_git merge feature-auth
mini_git branch feature-auth --delete
```

### 3. Simulated Team Development (Local)
```bash
# Create "central" repository
mkdir team_project && cd team_project
mini_git init
echo "Team Project" > README.md
mini_git add . && mini_git commit -m "Project start"

# Developer A
cd .. && mini_git clone team_project dev_alice
cd dev_alice
echo "Alice's feature" > feature_a.py
mini_git add . && mini_git commit -m "Add feature A"
mini_git push origin main

# Developer B
cd .. && mini_git clone team_project dev_bob
cd dev_bob
mini_git pull origin main  # Gets Alice's changes
echo "Bob's feature" > feature_b.py
mini_git add . && mini_git commit -m "Add feature B"
mini_git push origin main

# Alice syncs latest changes
cd ../dev_alice
mini_git pull origin main
ls  # See both features: feature_a.py, feature_b.py
```

### 4. Stash Workflow
```bash
# Working on something...
echo "Work in progress..." > unfinished.txt
mini_git add unfinished.txt

# Need to switch context quickly
mini_git stash push -m "WIP: new feature"
mini_git checkout other-branch

# Later, restore work
mini_git checkout main
mini_git stash pop  # Restores unfinished.txt

# Or manage multiple stashes
mini_git stash list
mini_git stash show 0
mini_git stash drop 0
```

## 🔧 Commands Reference

### Repository Operations
```bash
mini_git init                    # Initialize repository
mini_git clone <local_path> <dir> # Clone local repository
mini_git status                  # Show working directory status
```

### Staging & Committing
```bash
mini_git add <files>             # Stage files
mini_git add .                   # Stage all files
mini_git commit -m "message"     # Create commit
mini_git commit -m "msg" --author "Name <email>"  # With author
```

### History & Inspection
```bash
mini_git log                     # Show commit history
mini_git log --max-count 5       # Limit number of commits
mini_git diff                    # Show unstaged changes
mini_git diff <files>            # Diff specific files
```

### Branching
```bash
mini_git branch                  # List branches
mini_git branch <name>           # Create branch
mini_git branch <name> --delete  # Delete branch
mini_git checkout <branch>       # Switch branches
mini_git merge <branch>          # Merge branch into current
```

### Local Remotes
```bash
mini_git remote                  # List remotes
mini_git remote -v               # List with URLs
mini_git remote add <name> <local_path>  # Add local remote
mini_git remote remove <name>    # Remove remote
mini_git remote set-url <name> <path>    # Change remote URL
mini_git push <remote> <branch>  # Push to local remote
mini_git pull <remote> <branch>  # Pull from local remote
```

### Stashing
```bash
mini_git stash                   # Stash current changes
mini_git stash push -m "message" # Stash with message
mini_git stash list              # List all stashes
mini_git stash show              # Show latest stash
mini_git stash pop               # Apply and remove latest stash
mini_git stash drop              # Delete a stash
mini_git stash clear             # Delete all stashes
```

## 🧪 Testing

### Automated Test Suite
```bash
chmod +x test_minigit.sh
./test_minigit.sh
```

Tests cover:
- ✅ Basic repository operations
- ✅ Branching and merging
- ✅ Local clone/push/pull workflows
- ✅ Stash functionality
- ✅ Remote management
- ✅ Object store integrity
- ✅ Error handling

### Manual Testing
```bash
# Quick functionality test
mkdir test && cd test
mini_git init
echo "test" > file.txt
mini_git add . && mini_git commit -m "test"
mini_git log
```

## 🌐 Working with Network Remotes

Since Mini Git is local-only, here's how to work with GitHub/GitLab:

### Option 1: Develop Locally, Publish with Git
```bash
# Develop with Mini Git
mini_git add . && mini_git commit -m "Feature complete"

# Publish with standard Git
git init  # Initialize Git in same directory
git remote add origin https://github.com/user/repo.git
git add . && git commit -m "Feature complete"
git push origin main
```

### Option 2: Hybrid Workflow
```bash
# Use Mini Git for local development and learning
mini_git branch feature && mini_git checkout feature
mini_git add . && mini_git commit -m "Local development"

# Use Git for collaboration
git checkout main && git pull origin main
git merge feature && git push origin main
```

## 📁 Project Structure

```
mini_git/
├── src/
│   ├── main.rs           # CLI interface
│   ├── lib.rs            # Core types
│   ├── object_store.rs   # Git object storage
│   ├── utils.rs          # Repository utilities
│   └── commands/         # Command implementations
│       ├── init.rs       # Repository initialization
│       ├── add.rs        # Staging operations
│       ├── commit.rs     # Commit creation
│       ├── status.rs     # Working directory status
│       ├── log.rs        # History viewing
│       ├── branch.rs     # Branch management
│       ├── checkout.rs   # Branch switching
│       ├── merge.rs      # Three-way merge
│       ├── diff.rs       # File differences
│       ├── clone.rs      # Local cloning
│       ├── push.rs       # Local push operations
│       ├── pull.rs       # Local pull operations
│       ├── remote.rs     # Remote management
│       └── stash.rs      # Stash operations
├── Cargo.toml           # Dependencies
├── test_minigit.sh      # Test suite
└── README.md           # This file
```

## 🎓 Learning Objectives

By using Mini Git, you'll understand:

1. **Git Object Model**: How commits, trees, and blobs work
2. **Content Addressing**: Why Git uses SHA-1 hashes
3. **Distributed Architecture**: How multiple repositories sync
4. **Merge Algorithms**: Three-way merge and conflict resolution
5. **Index Mechanics**: How the staging area works
6. **Reference Management**: Branches, tags, and HEAD
7. **Data Integrity**: How Git ensures data consistency

## 🔧 Dependencies

```toml
[dependencies]
sha1 = "0.10"           # SHA-1 hashing for content addressing
serde = "1.0"           # Serialization framework
serde_json = "1.0"      # JSON support for objects
chrono = "0.4"          # Date and time handling
clap = "4.0"            # Command-line argument parsing
walkdir = "2.3"         # Directory tree traversal
flate2 = "1.0"          # Zlib compression for objects
```

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Write tests for your changes
4. Ensure all tests pass (`./test_minigit.sh`)
5. Commit your changes (`git commit -m 'Add amazing feature'`)
6. Push to the branch (`git push origin feature/amazing-feature`)
7. Open a Pull Request

## 📚 Further Learning

- [Pro Git Book](https://git-scm.com/book) - Complete Git reference
- [Git Internals](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain) - How Git works internally
- [Building Git](https://shop.oreilly.com/product/0636920041771.do) - Git implementation guide

## 📜 License

MIT License - see [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Git community for excellent design and documentation
- Rust community for amazing development tools
- Educational Git resources that inspired this implementation

***
**Made with ❤️ and 🦀 Rust ❤️ by [Aarambh Dev Hub](https://youtube.com/@aarambhdevhub)**

**🎯 Mini Git: Learn Git by building Git, one commit at a time!**

*Perfect for students, developers, and anyone curious about how Git really works under the hood.*
