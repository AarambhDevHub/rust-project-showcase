# 🔥 Ignitia Web Framework

<div align="center">

**A blazing fast, lightweight web framework for Rust that ignites your development journey.**

*Embodies the spirit of **Aarambh** (new beginnings) - the spark that ignites your web development journey*

**Built with ❤️ by [Aarambh Dev Hub](https://youtube.com/@aarambhdevhub)**
</div>
Ignitia embodies the spirit of **Aarambh** (new beginnings) - the spark that ignites your web development journey. Built for developers who demand speed, simplicity, and power.

- **🚀 Blazing Fast**: Built on Hyper and Tokio for maximum async performance
- **🪶 Lightweight**: Minimal overhead, maximum efficiency
- **🔥 Powerful**: Advanced routing, middleware, and built-in features
- **⚡ Energetic**: Modern APIs that energize your development
- **🎯 Developer-First**: Clean, intuitive, and productive
- **🛡️ Secure**: Built-in security features and best practices
- **🍪 Cookie Management**: Full-featured cookie handling with security attributes
- **🔧 Middleware**: Composable middleware architecture for cross-cutting concerns

***

## 📋 Table of Contents

- [Installation](#-installation)
- [Quick Start](#-quick-start)
- [Core Features](#-core-features)
- [Routing](#-routing)
- [Middleware](#-middleware)
- [Cookie Management](#-cookie-management)
- [Authentication](#-authentication)
- [Examples](#-examples)
- [API Reference](#-api-reference)
- [Testing](#-testing)
- [Performance](#-performance)
- [License](#-license)

***

## 🛠️ Installation

Add Ignitia to your `Cargo.toml`:

```toml
[dependencies]
ignitia = "0.1.0"
tokio = { version = "1.40", features = ["full"] }
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
tracing-subscriber = "0.3"
```

*Complete source code and project repository available upon request.*

***

## 🚀 Quick Start

Create your first Ignitia application:

```rust
use ignitia::{Router, Server, Request, Response, Result, handler_fn};

#[tokio::main]
async fn main() -> Result<()> {
    let router = Router::new()
        .get("/", handler_fn(hello_ignitia))
        .get("/users/:id", handler_fn(get_user))
        .post("/api/data", handler_fn(create_data));

    let server = Server::new(router, "127.0.0.1:3000".parse().unwrap());

    println!("🔥 Igniting server...");
    server.ignitia().await.unwrap(); // Custom ignitia method!
    Ok(())
}

async fn hello_ignitia(_req: Request) -> Result<Response> {
    Ok(Response::html(r#"
        <h1>🔥 Welcome to Ignitia!</h1>
        <p>Your web development journey starts here!</p>
    "#))
}

async fn get_user(req: Request) -> Result<Response> {
    let user_id = req.param("id").unwrap_or(&"unknown".to_string());
    Ok(Response::json(serde_json::json!({
        "message": "User ignited!",
        "user_id": user_id,
        "framework": "Ignitia 🔥"
    }))?)
}

async fn create_data(req: Request) -> Result<Response> {
    let data: serde_json::Value = req.json()?;
    Ok(Response::json(serde_json::json!({
        "status": "success",
        "received": data,
        "ignited_at": std::time::SystemTime::now()
    }))?)
}
```

***

## 🔥 Core Features

### **🛣️ Advanced Routing**

```rust
let router = Router::new()
    // Basic routes
    .get("/", handler_fn(home))
    .post("/api/users", handler_fn(create_user))
    .put("/api/users/:id", handler_fn(update_user))
    .delete("/api/users/:id", handler_fn(delete_user))

    // Route parameters
    .get("/users/:id/posts/:post_id", handler_fn(get_post))

    // Wildcard routes for static files
    .get("/*path", handler_fn(serve_static))

    // Custom 404 handler
    .not_found(handler_fn(not_found));
```

### **🍪 Built-in Cookie Management**

Secure, easy-to-use cookie handling with all security attributes:

```rust
use ignitia::{Cookie, SameSite};

// Set secure cookies
let session = Cookie::new("session", "user123")
    .path("/")
    .max_age(3600) // 1 hour
    .http_only()
    .secure()
    .same_site(SameSite::Lax);

let response = Response::text("Session ignited!")
    .add_cookie(session);

// Read cookies
async fn protected_route(req: Request) -> Result<Response> {
    let username = req.cookie("session")
        .unwrap_or("anonymous".to_string());

    Ok(Response::text(format!("Welcome back, {}!", username)))
}

// Remove cookies
let response = Response::text("Logged out")
    .remove_cookie("session");
```

### **🛡️ Powerful Middleware System**

Composable middleware for authentication, logging, CORS, and more:

```rust
use ignitia::middleware::{AuthMiddleware, CorsMiddleware, LoggerMiddleware};

let router = Router::new()
    // Global middleware
    .middleware(LoggerMiddleware)
    .middleware(CorsMiddleware::new()
        .allow_origin("https://example.com"))

    // Protected routes
    .middleware(AuthMiddleware::new("secret-token")
        .protect_path("/api/admin")
        .protect_path("/dashboard"))

    .get("/api/admin/users", handler_fn(admin_users))
    .get("/dashboard", handler_fn(dashboard));
```

### **🔐 Authentication & Authorization**

Built-in session management and role-based access control:

```rust
// Custom authentication middleware
#[derive(Clone)]
struct AuthMiddleware {
    protected_paths: Vec<String>,
}

#[async_trait]
impl Middleware for AuthMiddleware {
    async fn before(&self, req: &mut Request) -> Result<()> {
        let path = req.uri.path();

        if self.protected_paths.iter().any(|p| path.starts_with(p)) {
            let _session = req.cookie("session")
                .ok_or_else(|| Error::Unauthorized)?;
            // Validate session...
        }

        Ok(())
    }
}
```

***

## 🛣️ Routing

### **Parameter Extraction**

```rust
// Route: /users/:id/posts/:post_id
async fn get_post(req: Request) -> Result<Response> {
    let user_id = req.param("id").unwrap();
    let post_id = req.param("post_id").unwrap();

    Response::json(serde_json::json!({
        "user_id": user_id,
        "post_id": post_id
    }))
}
```

### **Query Parameters**

```rust
// URL: /search?q=rust&limit=10&page=1
async fn search(req: Request) -> Result<Response> {
    let query = req.query("q").unwrap_or(&"".to_string());
    let limit = req.query("limit")
        .and_then(|s| s.parse::<u32>().ok())
        .unwrap_or(20);
    let page = req.query("page")
        .and_then(|s| s.parse::<u32>().ok())
        .unwrap_or(1);

    // Perform search...
    Response::json(serde_json::json!({
        "query": query,
        "limit": limit,
        "page": page,
        "results": []
    }))
}
```

### **Wildcard Routes**

```rust
// Serve static files: /*path matches any path
.get("/*path", handler_fn(serve_static))

async fn serve_static(req: Request) -> Result<Response> {
    let path = req.param("path").unwrap();
    // Serve file from static directory with security checks
    serve_file_from_directory("./static", path).await
}
```

***

## 🔧 Middleware

### **Built-in Middleware**

| Middleware | Purpose | Usage |
|------------|---------|-------|
| `LoggerMiddleware` | Request/response logging | `.middleware(LoggerMiddleware)` |
| `CorsMiddleware` | Cross-origin resource sharing | `.middleware(CorsMiddleware::new())` |
| `AuthMiddleware` | Authentication | `.middleware(AuthMiddleware::new("token"))` |

### **Custom Middleware**

Create your own middleware by implementing the `Middleware` trait:

```rust
use ignitia::{Middleware, async_trait};

struct RateLimitMiddleware {
    max_requests: usize,
    window: Duration,
}

#[async_trait]
impl Middleware for RateLimitMiddleware {
    async fn before(&self, req: &mut Request) -> Result<()> {
        // Rate limiting logic
        Ok(())
    }

    async fn after(&self, res: &mut Response) -> Result<()> {
        // Add rate limit headers
        res.headers.insert(
            "X-RateLimit-Remaining",
            "99".parse().unwrap()
        );
        Ok(())
    }
}
```

***

## 🍪 Cookie Management

### **Setting Cookies**

```rust
use ignitia::{Cookie, SameSite};

// Session cookie
let session = Cookie::new("session", "abc123")
    .path("/")
    .max_age(3600)
    .http_only()
    .same_site(SameSite::Lax);

// Persistent cookie
let preferences = Cookie::new("theme", "dark")
    .path("/")
    .max_age(86400 * 30) // 30 days
    .same_site(SameSite::Strict);

// Secure cookie
let secure_token = Cookie::new("csrf_token", "xyz789")
    .path("/")
    .secure()
    .http_only()
    .same_site(SameSite::Strict);

Response::text("Cookies set!")
    .add_cookie(session)
    .add_cookie(preferences)
    .add_cookie(secure_token)
```

### **Reading Cookies**

```rust
async fn handle_request(req: Request) -> Result<Response> {
    // Get all cookies
    let cookies = req.cookies();

    // Get specific cookie
    let session = req.cookie("session");

    // Check if cookie exists
    if cookies.contains("user_preferences") {
        // Handle with preferences
    }

    Response::json(cookies.all())
}
```

### **Cookie Security**

```rust
// Production-ready secure cookie
let secure_session = Cookie::new("session", session_id)
    .path("/")
    .max_age(3600)
    .http_only()        // Prevent XSS
    .secure()           // HTTPS only
    .same_site(SameSite::Strict); // CSRF protection
```

***

## 🔐 Authentication

### **Session-Based Authentication**

```rust
async fn login(req: Request) -> Result<Response> {
    let credentials: LoginForm = req.json()?;

    if validate_credentials(&credentials).await? {
        let session_token = generate_session_token();

        let session_cookie = Cookie::new("session", session_token)
            .path("/")
            .max_age(3600)
            .http_only()
            .same_site(SameSite::Lax);

        Ok(Response::json(serde_json::json!({
            "status": "success",
            "message": "Login successful"
        }))?.add_cookie(session_cookie))
    } else {
        Err(Error::Unauthorized)
    }
}

async fn logout(_req: Request) -> Result<Response> {
    Ok(Response::json(serde_json::json!({
        "status": "success",
        "message": "Logged out"
    }))?.remove_cookie("session"))
}
```

### **Protected Routes with Middleware**

```rust
let router = Router::new()
    .middleware(AuthMiddleware::new()
        .protect_paths(vec!["/dashboard", "/admin", "/api/protected"]))

    // These routes are automatically protected
    .get("/dashboard", handler_fn(dashboard))
    .get("/admin", handler_fn(admin_panel))
    .get("/api/protected/data", handler_fn(protected_data));
```

***

## 📚 Examples

Ignitia comes with comprehensive examples to get you started:

### **🏠 Basic Server**
Demonstrates basic routing, JSON responses, and parameter extraction.

### **🔐 Authentication System**
Complete login/logout system with session management and protected routes.

### **🛡️ Middleware Showcase**
Advanced authentication using middleware with role-based access control.

### **🍪 Cookie Management**
Comprehensive cookie handling with security features and session management.

### **⚡ Custom Middleware**
Rate limiting, request validation, security headers, and custom middleware examples.

### **📊 JSON API**
RESTful API for managing todos with in-memory storage and CRUD operations.

### **📁 File Server**
Static file server with security features, MIME type detection, and directory traversal protection.

### **🎭 Middleware Demo**
CORS, authentication, and logging middleware examples.

***

## 🔌 API Reference

### **Router**

| Method | Description |
|--------|-------------|
| `Router::new()` | Create a new router |
| `.get(path, handler)` | Add GET route |
| `.post(path, handler)` | Add POST route |
| `.put(path, handler)` | Add PUT route |
| `.delete(path, handler)` | Add DELETE route |
| `.middleware(middleware)` | Add middleware |
| `.not_found(handler)` | Set 404 handler |

### **Request**

| Method | Description | Example |
|--------|-------------|---------|
| `req.param(key)` | Get route parameter | `req.param("id")` |
| `req.query(key)` | Get query parameter | `req.query("limit")` |
| `req.header(key)` | Get header value | `req.header("User-Agent")` |
| `req.json<T>()` | Parse JSON body | `req.json::<User>()?` |
| `req.cookies()` | Get all cookies | `req.cookies().all()` |
| `req.cookie(key)` | Get specific cookie | `req.cookie("session")` |

### **Response**

| Method | Description | Example |
|--------|-------------|---------|
| `Response::text(content)` | Text response | `Response::text("Hello")` |
| `Response::html(content)` | HTML response | `Response::html("<h1>Hi</h1>")` |
| `Response::json(data)` | JSON response | `Response::json(user)?` |
| `Response::not_found()` | 404 response | `Response::not_found()` |
| `.add_cookie(cookie)` | Add cookie | `.add_cookie(session)` |
| `.remove_cookie(name)` | Remove cookie | `.remove_cookie("session")` |

### **Cookie Builder**

| Method | Description | Example |
|--------|-------------|---------|
| `Cookie::new(name, value)` | Create cookie | `Cookie::new("user", "john")` |
| `.path(path)` | Set cookie path | `.path("/")` |
| `.max_age(seconds)` | Set expiration | `.max_age(3600)` |
| `.secure()` | HTTPS only | `.secure()` |
| `.http_only()` | No JavaScript access | `.http_only()` |
| `.same_site(policy)` | CSRF protection | `.same_site(SameSite::Lax)` |

### **Middleware Trait**

```rust
#[async_trait]
pub trait Middleware: Send + Sync {
    async fn before(&self, req: &mut Request) -> Result<()> { Ok(()) }
    async fn after(&self, res: &mut Response) -> Result<()> { Ok(()) }
}
```

***

## 🧪 Testing

### **Run Tests**

```bash
# Run all tests
cargo test

# Run with verbose output
cargo test -- --nocapture

# Run integration tests
cargo test --test integration_test

# Run specific test
cargo test test_authentication
```

### **Example Test**

```rust
#[tokio::test]
async fn test_cookie_authentication() {
    let router = Router::new()
        .get("/protected", handler_fn(protected_route));

    // Test without cookie (should fail)
    let req = Request::new(Method::GET, "/protected".parse().unwrap(), /*...*/);
    let response = router.handle(req).await;
    assert!(matches!(response, Err(Error::Unauthorized)));

    // Test with valid cookie (should succeed)
    let mut req_with_cookie = Request::new(/*...*/);
    req_with_cookie.headers.insert(
        "Cookie",
        "session=valid_token".parse().unwrap()
    );
    let response = router.handle(req_with_cookie).await.unwrap();
    assert_eq!(response.status, StatusCode::OK);
}
```

***

## 🎯 Performance

### **Benchmarks**

- **Zero-copy**: Efficient request/response handling with minimal allocations
- **Async**: Built on Tokio for excellent concurrency
- **Lightweight**: ~50KB binary overhead
- **Fast compilation**: Small dependency tree for quick builds
- **Memory efficient**: Smart use of Arc and shared state

### **Performance Tips**

```rust
// Use connection pooling
let router = Router::new()
    .middleware(ConnectionPoolMiddleware::new())

    // Cache static responses
    .middleware(CacheMiddleware::new())

    // Compress responses
    .middleware(CompressionMiddleware::new());
```

***

## 🔒 Security Features

### **Built-in Security**

- ✅ **Path traversal protection** in static file serving
- ✅ **CORS middleware** for cross-origin requests
- ✅ **Authentication middleware** with config
