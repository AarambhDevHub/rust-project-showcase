# Toy Language Interpreter

A complete **mini compiler/interpreter** implementation written in **Rust** featuring lexical analysis, parsing, Abstract Syntax Tree (AST) generation, and execution of a custom toy programming language.

## 🚀 Features

### Core Language Features
- **Variables & Data Types**: Numbers, strings, booleans, and nil
- **Arithmetic Operations**: `+`, `-`, `*`, `/`, `%` (modulo)
- **Comparison Operations**: `>`, `>=`, `<`, `<=`, `==`, `!=`
- **Logical Operations**: `&&`, `||`, `!`
- **Control Flow**: `if/else` statements, `while` loops
- **Functions**: Declaration, parameters, return values, recursion
- **Variable Scoping**: Lexical scoping with proper environment management
- **String Concatenation**: Automatic type coercion for mixed-type operations

### Advanced Features
- **Recursive Functions**: Full support for recursive algorithms
- **Error Handling**: Comprehensive error messages with line/column information
- **REPL Mode**: Interactive Read-Eval-Print Loop for testing
- **File Execution**: Run `.toy` files directly with proper file extension validation
- **Comment Support**: Single-line comments using `//`
- **Mathematical Functions**: Built-in support for complex mathematical operations

### Implementation Features
- **Complete Lexer**: Tokenization with position tracking
- **Recursive Descent Parser**: Produces clean Abstract Syntax Trees
- **Tree-Walking Interpreter**: Direct AST execution with environment management
- **Memory Safe**: All implemented in safe Rust with proper error handling

## 📁 Project Structure

```
toy_lang/
├── Cargo.toml              # Rust project configuration
├── README.md              # This file
├── src/
│   ├── main.rs            # Entry point and REPL
│   ├── lib.rs             # Library exports
│   ├── lexer.rs           # Tokenization and lexical analysis
│   ├── parser.rs          # Recursive descent parser
│   ├── ast.rs             # Abstract Syntax Tree definitions
│   └── interpreter.rs     # Tree-walking interpreter
└── examples/
    ├── hello.toy          # Basic syntax examples
    ├── fibonacci.toy      # Recursive algorithms
    ├── functions.toy      # Advanced function examples
    ├── calculator.toy     # Mathematical operations
    └── algorithms.toy     # Complex algorithms and patterns
```

## 🛠️ Installation

### Prerequisites
- **Rust** 1.70+ (2021 edition)
- **Cargo** (comes with Rust)

### Build from Source
```bash
# Clone the repository
cd toy_lang

# Build the project
cargo build --release

# Or run directly
cargo run
```

## 📖 Usage

### Interactive REPL Mode
Start the interactive interpreter:
```bash
cargo run
```

Example REPL session:
```
Toy Language REPL v0.1.0
Type 'exit' to quit
toy> let x = 10;
toy> let y = 20;
toy> print("Sum: " + (x + y));
Sum: 30
toy> exit
```

### File Execution
Run a `.toy` file:
```bash
cargo run examples/hello.toy
cargo run examples/fibonacci.toy
cargo run examples/functions.toy
```

### Command Line Options
```bash
# Run REPL (no arguments)
cargo run

# Execute a file
cargo run <filename.toy>

# Multiple files not supported - run one at a time
```

## 🔤 Language Syntax

### Variables and Data Types
```javascript
// Variable declaration
let name = "Alice";
let age = 25;
let pi = 3.14159;
let isActive = true;
let empty = nil;
```

### Arithmetic and Logic
```javascript
// Arithmetic
let sum = 10 + 5;        // 15
let product = 4 * 7;     // 28
let remainder = 17 % 5;  // 2

// Comparisons
let isEqual = (5 == 5);  // true
let isGreater = (10 > 3); // true

// Logical operations
let both = true && false; // false
let either = true || false; // true
```

### Control Flow
```javascript
// If statements
if (age >= 18) {
    print("You are an adult");
} else {
    print("You are a minor");
}

// While loops
let i = 0;
while (i < 5) {
    print("Count: " + i);
    i = i + 1;
}
```

### Functions
```javascript
// Function declaration
function greet(name, age) {
    print("Hello, " + name + "! You are " + age + " years old.");
}

// Function with return value
function factorial(n) {
    if (n <= 1) {
        return 1;
    }
    return n * factorial(n - 1);
}

// Function calls
greet("Bob", 30);
let result = factorial(5);
print("5! = " + result); // 5! = 120
```

### String Operations
```javascript
// String concatenation with automatic type conversion
let name = "Alice";
let age = 25;
print("Name: " + name + ", Age: " + age);
// Output: Name: Alice, Age: 25
```

### Comments
```javascript
// This is a single-line comment
let x = 10; // End-of-line comment
```

## 📋 Example Programs

### Hello World (`examples/hello.toy`)
```javascript
print("Hello, World!");

let name = "Alice";
print("Hello, " + name + "!");

let x = 10;
let y = 20;
print("Sum: " + (x + y));
```

### Fibonacci Sequence (`examples/fibonacci.toy`)
```javascript
function fib(n) {
    if (n <= 1) {
        return n;
    }
    return fib(n - 1) + fib(n - 2);
}

print("Fibonacci of 10 is: " + fib(10));
```

### Calculator (`examples/calculator.toy`)
```javascript
function power(base, exponent) {
    let result = 1;
    let i = 0;
    while (i < exponent) {
        result = result * base;
        i = i + 1;
    }
    return result;
}

print("2^8 = " + power(2, 8)); // 256
```

## 🎯 Advanced Examples

### Prime Number Detection
```javascript
function isPrime(n) {
    if (n <= 1) {
        return false;
    }

    let i = 2;
    while (i * i <= n) {
        if (n % i == 0) {
            return false;
        }
        i = i + 1;
    }
    return true;
}
```

### Collatz Conjecture
```javascript
function collatz(n) {
    let steps = 0;
    while (n != 1) {
        if (n % 2 == 0) {
            n = n / 2;
        } else {
            n = n * 3 + 1;
        }
        steps = steps + 1;
    }
    return steps;
}
```

## 🔧 Technical Details

### Architecture
1. **Lexer** (`lexer.rs`): Converts source code into tokens
2. **Parser** (`parser.rs`): Builds Abstract Syntax Tree from tokens
3. **AST** (`ast.rs`): Defines tree node structures and value types
4. **Interpreter** (`interpreter.rs`): Executes the AST with environment management

### Error Handling
- **Lexical Errors**: Unknown characters, unterminated strings
- **Parse Errors**: Syntax errors with context
- **Runtime Errors**: Type mismatches, undefined variables, division by zero
- **All errors include line and column information**

### Performance Characteristics
- **Tree-walking interpreter**: Direct AST execution (not bytecode)
- **Recursive descent parsing**: O(n) parsing time
- **Variable lookup**: O(1) in current scope, O(d) across scope chain
- **Memory usage**: Proportional to AST size and call stack depth

## 🚀 Running Examples

```bash
# Basic examples
cargo run examples/hello.toy
cargo run examples/fibonacci.toy
cargo run examples/functions.toy

# Advanced examples
cargo run examples/calculator.toy
cargo run examples/algorithms.toy
```

## 🎓 Educational Value

This project demonstrates:
- **Compiler Construction**: Lexing, parsing, and interpretation phases
- **Language Design**: Syntax choices and semantic decisions
- **Rust Programming**: Advanced Rust features like enums, pattern matching, and borrowing
- **Algorithm Implementation**: Recursive algorithms and mathematical computations
- **Software Architecture**: Clean separation of concerns across modules

## 🔮 Future Enhancements

Potential improvements and extensions:
- **Arrays/Lists**: Data structure support
- **Objects/Classes**: Object-oriented programming features
- **Modules/Imports**: Code organization and reuse
- **Standard Library**: Built-in functions for common operations
- **Bytecode Compilation**: Performance improvements
- **Garbage Collection**: Automatic memory management
- **Type System**: Static or dynamic typing improvements

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature-name`
3. Make your changes and add tests
4. Commit changes: `git commit -am 'Add feature'`
5. Push to branch: `git push origin feature-name`
6. Submit a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Inspired by **"Crafting Interpreters"** by Robert Nystrom
- Built with the **Rust programming language**
- Thanks to the Rust community for excellent documentation and tools

***

**Happy Coding! 🎉**

For questions, issues, or contributions, please visit the [GitHub repository](https://github.com/yourusername/toy_lang) or open an issue.
