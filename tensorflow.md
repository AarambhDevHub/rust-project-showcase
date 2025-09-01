# 🦀 Mini TensorFlow - Deep Learning Library in Rust

A comprehensive, high-performance deep learning library implemented in Rust, inspired by TensorFlow and PyTorch. Built with safety, speed, and ergonomics in mind.

## 🚀 Features

- **🔢 Tensor Operations**: Multi-dimensional arrays with broadcasting support
- **🧠 Neural Networks**: Dense, Convolutional, and Activation layers
- **📊 Computation Graph**: Dynamic graph execution with forward pass
- **⚡ SIMD & Parallel**: Vectorized operations and multi-core processing
- **💾 Model Serialization**: Save/load models in JSON and binary formats
- **📈 Data Loading**: CSV support, synthetic datasets, and batch processing
- **🔧 Optimizers**: SGD with momentum and Adam optimizer
- **🛡️ Memory Safety**: Zero-cost abstractions with Rust's ownership system

## 📋 Table of Contents

- [Architecture](#architecture)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [API Documentation](#api-documentation)
- [Examples](#examples)
- [Performance](#performance)
- [Contributing](#contributing)
- [License](#license)

## 🏗️ Architecture

### System Overview
```
┌─────────────────────────────────────────────────────────────────┐
│                     Mini TensorFlow Library                     │
├─────────────────────────────────────────────────────────────────┤
│  Examples Layer                                                 │
│  ┌─────────────┬─────────────┬─────────────┬─────────────────┐  │
│  │ Sequential  │ CNN Example │ Data Loading│ SIMD Benchmark  │  │
│  │ Models      │ & Training  │ & Batching  │ & Performance   │  │
│  └─────────────┴─────────────┴─────────────┴─────────────────┘  │
├─────────────────────────────────────────────────────────────────┤
│  High-Level API                                                 │
│  ┌─────────────┬─────────────┬─────────────┬─────────────────┐  │
│  │ Sequential  │ Layer       │ DataLoader  │ Model           │  │
│  │ Container   │ Abstraction │ & Dataset   │ Serialization   │  │
│  └─────────────┴─────────────┴─────────────┴─────────────────┘  │
├─────────────────────────────────────────────────────────────────┤
│  Core Components                                                │
│  ┌─────────────┬─────────────┬─────────────┬─────────────────┐  │
│  │ Layers      │ Convolution │ Optimizers  │ Autograd        │  │
│  │ (Dense,     │ (Conv2D,    │ (SGD,       │ (Variables,     │  │
│  │ Activations)│ MaxPool2D)  │ Adam)       │ Gradients)      │  │
│  └─────────────┴─────────────┴─────────────┴─────────────────┘  │
├─────────────────────────────────────────────────────────────────┤
│  Computation Engine                                             │
│  ┌─────────────┬─────────────┬─────────────┬─────────────────┐  │
│  │ Tensor      │ Graph       │ SIMD        │ Parallel        │  │
│  │ Operations  │ Execution   │ Operations  │ Computing       │  │
│  └─────────────┴─────────────┴─────────────┴─────────────────┘  │
├─────────────────────────────────────────────────────────────────┤
│  Foundation                                                     │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │            Rust Memory Safety & Performance               │  │
│  │     (Zero-cost abstractions, RAII, Ownership model)      │  │
│  └───────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

### Data Flow Architecture
```
Input Data → Tensor → Layer Chain → Output → Loss → Optimizer → Updated Parameters
     │         │         │           │        │         │              │
     │         │         │           │        │         │              │
     ▼         ▼         ▼           ▼        ▼         ▼              ▼
  ┌─────┐ ┌─────────┐ ┌─────────┐ ┌─────┐ ┌──────┐ ┌────────┐ ┌─────────────┐
  │ CSV │ │ Tensor  │ │ Conv2D  │ │ Loss│ │ SGD/ │ │ Params │ │ Serialized  │
  │Files│ │ Ops     │ │ Dense   │ │ Calc│ │ Adam │ │ Update │ │ Model       │
  │ ... │ │ SIMD    │ │ ReLU    │ │ ... │ │ ... │ │ ...    │ │ JSON/Binary │
  └─────┘ └─────────┘ └─────────┘ └─────┘ └──────┘ └────────┘ └─────────────┘
```

### Tensor Computation Flow
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           Tensor Operations                                 │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                          Shape Validation                                  │
│                     (Broadcasting, Dimension checks)                       │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────┬─────────────────┬─────────────────┬─────────────────────┐
│ Regular Ops     │ SIMD Optimized  │ Parallel Ops    │ Specialized Ops     │
│ - Element-wise  │ - f64x4 vectors │ - Rayon threads │ - Matrix multiply   │
│ - Single thread │ - AVX/SSE       │ - Multi-core    │ - Convolution       │
│ - Standard loop │ - 4x throughput │ - Work stealing │ - Activation funcs  │
└─────────────────┴─────────────────┴─────────────────┴─────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                            Result Tensor                                   │
│                         (New shape, data)                                  │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Neural Network Layer Architecture
```
Sequential Model Container
│
├── Layer 1: Input Processing
│   ├── Conv2D(in_channels=1, out_channels=32, kernel=3x3)
│   ├── ReLU activation
│   └── MaxPool2D(kernel=2x2, stride=2)
│
├── Layer 2: Feature Extraction
│   ├── Conv2D(in_channels=32, out_channels=64, kernel=3x3)
│   ├── ReLU activation
│   └── MaxPool2D(kernel=2x2, stride=2)
│
├── Layer 3: Flattening
│   └── Flatten(4D → 2D conversion)
│
├── Layer 4: Classification Head
│   ├── Dense(features_in=1600, features_out=128)
│   ├── ReLU activation
│   ├── Dense(features_in=128, features_out=10)
│   └── Softmax activation
│
└── Output: Probability Distribution [batch_size, num_classes]
```

### Memory Management Flow
```
Stack Memory                  Heap Memory                   GPU/SIMD
┌─────────────┐              ┌─────────────┐              ┌─────────────┐
│ References  │              │ Tensor Data │              │ Vectorized  │
│ &Tensor     │─────────────▶│ Vec<f64>    │─────────────▶│ Operations  │
│ &mut Tensor │              │ Shape info  │              │ f64x4 SIMD  │
│ Temporaries │              │ Gradients   │              │ Parallel    │
└─────────────┘              └─────────────┘              └─────────────┘
      │                             │                             │
      ▼                             ▼                             ▼
  Zero-copy                    RAII cleanup               Hardware acceleration
  borrowing                   Automatic drop              AVX2/FMA instructions
```

## 📦 Installation

### Prerequisites
- Rust 1.70+ (2021 edition)
- Cargo package manager

### Dependencies
Add to your `Cargo.toml`:

```toml
[dependencies]
mini_tensorflow = "0.1.0"
rand = "0.8"
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
bincode = "1.3"
csv = "1.2"
rayon = "1.7"
num-traits = "0.2"

[target.'cfg(target_arch = "x86_64")'.dependencies]
wide = "0.7"  # SIMD operations
```

*Complete source code and project repository available upon request.*

## 🚀 Quick Start

### Basic Tensor Operations
```rust
use mini_tensorflow::Tensor;

// Create tensors
let a = Tensor::new(vec![1.0, 2.0, 3.0, 4.0], vec![2, 2]);
let b = Tensor::new(vec![5.0, 6.0, 7.0, 8.0], vec![2, 2]);

// Basic operations
let sum = a.add(&b);                    // Element-wise addition
let product = a.matmul(&b);             // Matrix multiplication
let activated = a.relu();               // ReLU activation

println!("Sum: {}", sum);
println!("Matrix product: {}", product);
println!("ReLU activated: {}", activated);
```

### Building Neural Networks
```rust
use mini_tensorflow::{Sequential, Dense, ReLU, Softmax};

// Create a multi-layer perceptron
let model = Sequential::new()
    .add(Dense::new(784, 256))    // Input layer
    .add(ReLU::new())
    .add(Dense::new(256, 128))    // Hidden layer
    .add(ReLU::new())
    .add(Dense::new(128, 10))     // Output layer
    .add(Softmax::new());

// Display model architecture
model.summary();

// Forward pass
let input = Tensor::random(vec![1, 784]);
let output = model.forward(input);
println!("Predictions: {}", output);
```

### Convolutional Neural Networks
```rust
use mini_tensorflow::{Sequential, Conv2D, MaxPool2D, Flatten, Dense, ReLU, Softmax};

// Create CNN for image classification
let cnn = Sequential::new()
    .add(Conv2D::new(1, 32, 3))      // 1→32 channels, 3×3 kernel
    .add(ReLU::new())
    .add(MaxPool2D::new(2))          // 2×2 pooling
    .add(Conv2D::new(32, 64, 3))     // 32→64 channels
    .add(ReLU::new())
    .add(MaxPool2D::new(2))
    .add(Flatten::new())
    .add(Dense::new(1600, 128))      // Flattened features → 128
    .add(ReLU::new())
    .add(Dense::new(128, 10))        // 10 classes
    .add(Softmax::new());

// Process 28×28 image
let image = Tensor::random(vec![1, 1, 28, 28]);
let predictions = cnn.forward(image);
```

### Data Loading & Training
```rust
use mini_tensorflow::{Dataset, DataLoader, SGD, Optimizer};

// Load data from CSV
let dataset = Dataset::from_csv(
    "data.csv",
    vec![0, 1, 2, 3],  // feature columns
    4,                 // target column
    true               // has header
)?;

// Create data loader with batching
let mut loader = DataLoader::new(dataset, 32)
    .with_shuffle(true);

// Training loop
let mut optimizer = SGD::new(0.01);

for (batch_data, batch_labels) in loader.iter() {
    // Forward pass
    let predictions = model.forward(batch_data[0].clone());

    // Compute loss (simplified)
    let loss = compute_loss(&predictions, &batch_labels[0]);

    // Update parameters (with real gradients in production)
    let mut params = model.parameters_mut();
    optimizer.step(&mut params, &gradients);
}
```

### Model Persistence
```rust
use mini_tensorflow::Saveable;

// Save model
model.save("trained_model.json")?;   // Human readable
model.save("trained_model.bin")?;    // Compact binary

// Load model
let mut new_model = Sequential::new()
    .add(Dense::new(784, 256))
    .add(ReLU::new());

new_model.load("trained_model.json")?;
```

## 📚 API Documentation

### Core Components

#### Tensor
```rust
impl Tensor {
    // Construction
    pub fn new(data: Vec<f64>, shape: Vec<usize>) -> Self;
    pub fn zeros(shape: Vec<usize>) -> Self;
    pub fn ones(shape: Vec<usize>) -> Self;
    pub fn random(shape: Vec<usize>) -> Self;

    // Operations
    pub fn add(&self, other: &Tensor) -> Tensor;
    pub fn mul(&self, other: &Tensor) -> Tensor;
    pub fn matmul(&self, other: &Tensor) -> Tensor;
    pub fn relu(&self) -> Tensor;
    pub fn sigmoid(&self) -> Tensor;
    pub fn softmax(&self) -> Tensor;

    // Shape manipulation
    pub fn reshape(&self, new_shape: Vec<usize>) -> Self;
    pub fn transpose(&self) -> Self;

    // Utilities
    pub fn sum(&self) -> f64;
    pub fn mean(&self) -> f64;
}
```

#### Sequential Model
```rust
impl Sequential {
    pub fn new() -> Self;
    pub fn add<L: Layer + 'static>(self, layer: L) -> Self;
    pub fn forward(&self, input: Tensor) -> Tensor;
    pub fn parameters(&self) -> Vec<&Tensor>;
    pub fn summary(&self);
}
```

#### Layers
```rust
// Dense layer
Dense::new(input_size: usize, output_size: usize) -> Dense;

// Convolutional layer
Conv2D::new(in_channels: usize, out_channels: usize, kernel_size: usize) -> Conv2D;

// Pooling layer
MaxPool2D::new(kernel_size: usize) -> MaxPool2D;

// Activation layers
ReLU::new() -> ReLU;
Sigmoid::new() -> Sigmoid;
Softmax::new() -> Softmax;
```

#### Optimizers
```rust
// Stochastic Gradient Descent
SGD::new(learning_rate: f64) -> SGD;
SGD::with_momentum(learning_rate: f64, momentum: f64) -> SGD;

// Adam optimizer
Adam::new(learning_rate: f64) -> Adam;
Adam::with_params(lr: f64, beta1: f64, beta2: f64, epsilon: f64) -> Adam;

// Training step
optimizer.step(parameters: &mut [&mut Tensor], gradients: &[&Tensor]);
```

## 🎯 Examples

The project includes comprehensive examples:

### 1. Basic Operations
Demonstrates tensor creation, arithmetic, and shape manipulation.

### 2. Sequential Neural Network
Multi-layer perceptron with dense layers and activations.

### 3. Convolutional Neural Network
Image classification with Conv2D, pooling, and dense layers.

### 4. Data Loading Pipeline
CSV loading, synthetic datasets, batching, and normalization.

### 5. Performance Benchmarks
SIMD vs regular operations, parallel computing benchmarks.

### 6. Model Serialization
Saving and loading trained models in JSON and binary formats.

## ⚡ Performance

### Benchmarks (on typical hardware)
```
Operation               Regular    SIMD      Parallel   Speedup
──────────────────────────────────────────────────────────────
Vector Addition (1M)    18ms      12ms      8ms        2.2x
Matrix Multiply (500²)   5.0s      N/A       1.1s       4.5x
Element-wise Multiply    20ms      15ms      8ms        2.5x
ReLU Activation         15ms      9ms       N/A        1.7x
Memory Bandwidth        1.6GB/s   5.7GB/s   N/A        3.6x
```

### Optimization Features
- **SIMD Vectorization**: 4-element f64 operations using AVX2
- **Parallel Computing**: Multi-threaded operations via Rayon
- **Memory Efficiency**: Zero-copy operations where possible
- **Cache Optimization**: Contiguous memory layout

### Platform Support
- **x86_64**: Full SIMD optimization enabled
- **ARM64**: Parallel operations (SIMD fallback)
- **Other**: Regular operations with parallel support

## 🔧 Advanced Features

### Custom Layer Implementation
```rust
use mini_tensorflow::{Tensor, Layer};

#[derive(Debug, Clone)]
struct BatchNorm {
    gamma: Tensor,
    beta: Tensor,
    running_mean: Tensor,
    running_var: Tensor,
    epsilon: f64,
}

impl Layer for BatchNorm {
    fn forward(&self, input: &Tensor) -> Tensor {
        // Implement batch normalization
        // normalized = (input - mean) / sqrt(var + epsilon)
        // output = gamma * normalized + beta
        todo!()
    }

    // Implement other required methods...
}
```

### Performance Optimization Tips
```rust
use mini_tensorflow::{SIMDOps, ParallelOps};

// Use SIMD for element-wise operations on large tensors
let result = tensor_a.simd_add(&tensor_b);

// Use parallel operations for matrix multiplication
let matmul = matrix_a.parallel_matmul(&matrix_b);

// Prefer in-place operations when possible
tensor.data.iter_mut().for_each(|x| *x = x.max(0.0)); // In-place ReLU
```

## 🏗️ Architecture Decisions

### Memory Management
- **Ownership Model**: Rust's ownership system prevents data races
- **RAII**: Automatic resource cleanup via Drop trait
- **Zero-Copy**: References and borrowing minimize allocations
- **Contiguous Layout**: Vec<f64> for cache-friendly access patterns

### Numerical Stability
- **f64 Precision**: Double precision throughout for accuracy
- **Overflow Protection**: Checked operations in debug builds
- **Numerical Algorithms**: Stable implementations of softmax, etc.

### Extensibility
- **Trait System**: Layer trait enables custom implementations
- **Generic Design**: Template-like functionality without runtime cost
- **Module System**: Clean separation of concerns

## 🧪 Testing

```bash
# Run all tests
cargo test

# Run with full output
cargo test -- --nocapture

# Run specific test
cargo test tensor_operations

# Benchmark tests
cargo bench
```

## 🤝 Contributing

### Development Setup
1. Fork the repository
2. Create a feature branch: `git checkout -b feature-name`
3. Make changes and add tests
4. Run tests: `cargo test`
5. Submit a pull request

### Code Style
- Follow Rust standard formatting: `cargo fmt`
- Run clippy lints: `cargo clippy`
- Add documentation for public APIs
- Include examples in documentation

### Areas for Contribution
- **Gradient Computation**: Implement full automatic differentiation
- **More Optimizers**: RMSprop, AdaGrad, etc.
- **Advanced Layers**: LSTM, Transformer, BatchNorm
- **GPU Support**: CUDA or OpenCL backends
- **Model Formats**: ONNX import/export
- **Distributed Training**: Multi-node support

## 🐛 Known Limitations

- **Gradients**: Currently simplified backward pass implementation
- **GPU**: CPU-only, no GPU acceleration yet
- **Dynamic Shapes**: Limited dynamic graph support
- **Memory**: Large models may hit memory constraints
- **Precision**: Single precision (f32) not yet supported

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **PyTorch**: API design inspiration
- **Candle**: Rust ML framework reference
- **Rayon**: Parallel computing library
- **Serde**: Serialization framework
- **The Rust Community**: Excellent tooling and libraries

## 📞 Support

- **Documentation**: Comprehensive examples and API reference included
- **YouTube Demos**: *Coming soon on [Aarambh Dev Hub](https://youtube.com/@aarambhdevhub)*
- **Issues**: Available for discussion and feedback

***

**Made with ❤️ and 🦀 Rust ❤️ by [Aarambh Dev Hub](https://youtube.com/@aarambhdevhub)**

*Mini TensorFlow demonstrates that systems programming languages can be both safe AND fast for machine learning workloads.*
