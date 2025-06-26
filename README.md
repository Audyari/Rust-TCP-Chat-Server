# Rust TCP Chat Server - Task Breakdown

## Proyek: Chat Server Sederhana dengan Rust Concurrency

### Tujuan Pembelajaran
- Memahami TCP networking di Rust
- Implementasi multi-threading dengan `std::thread`
- Message passing menggunakan `mpsc channels`
- Manajemen shared state yang aman
- Error handling dalam concurrent environment

---

## Phase 1: Setup dan Struktur Dasar

### Task 1.1: Project Initialization
- [ ] Buat project baru dengan `cargo new chat_server`
- [ ] Setup `Cargo.toml` dengan dependencies yang diperlukan
- [ ] Buat struktur folder dasar (`src/main.rs`, `src/lib.rs`)
- [ ] Setup logging dengan `env_logger` atau `tracing`

### Task 1.2: Define Core Data Structures
- [ ] Buat struct `Client` untuk menyimpan informasi client
  - [ ] Field `id: usize`
  - [ ] Field `name: String`
  - [ ] Field `sender: mpsc::Sender<String>`
- [ ] Buat enum `Message` untuk berbagai jenis pesan
  - [ ] `ClientJoined(Client)`
  - [ ] `ClientLeft(usize)`
  - [ ] `Broadcast(String)`
  - [ ] `DirectMessage { from: usize, to: usize, content: String }`
- [ ] Buat struct `Server` untuk state management

---

## Phase 2: Basic TCP Server

### Task 2.1: TCP Listener Setup
- [ ] Import `std::net::TcpListener`
- [ ] Bind server ke `127.0.0.1:7878`
- [ ] Handle binding errors dengan proper error handling
- [ ] Print server status ke console

### Task 2.2: Accept Single Connection
- [ ] Implement basic `accept()` loop
- [ ] Handle incoming connections
- [ ] Read data dari client menggunakan `BufReader`
- [ ] Echo pesan kembali ke client (proof of concept)
- [ ] Test dengan `telnet localhost 7878`

### Task 2.3: Handle Connection Errors
- [ ] Wrap connection handling dalam `Result`
- [ ] Handle `std::io::Error` dengan graceful degradation
- [ ] Log connection events
- [ ] Implement basic cleanup

---

## Phase 3: Multi-threading Foundation

### Task 3.1: Thread-per-Client Model
- [ ] Spawn thread baru untuk setiap client connection
- [ ] Move connection handling ke separate function
- [ ] Pass `TcpStream` ke thread handler
- [ ] Test multiple concurrent connections

### Task 3.2: Thread Communication Setup
- [ ] Create main communication channel `mpsc::channel()`
- [ ] Pass `Sender` ke setiap client thread
- [ ] Setup receiver di main thread
- [ ] Implement basic message passing test

### Task 3.3: Client ID Management
- [ ] Implement atomic counter untuk client IDs
- [ ] Use `std::sync::atomic::AtomicUsize`
- [ ] Assign unique ID ke setiap client
- [ ] Track active client count

---

## Phase 4: Message Broadcasting System

### Task 4.1: Client Registry
- [ ] Create `HashMap<usize, mpsc::Sender<String>>` untuk client lookup
- [ ] Wrap dalam `Arc<Mutex<>>` untuk thread safety
- [ ] Implement add/remove client functions
- [ ] Handle disconnected clients cleanup

### Task 4.2: Message Router Thread
- [ ] Spawn dedicated thread untuk message routing
- [ ] Receive messages dari main channel
- [ ] Route messages berdasarkan message type
- [ ] Implement broadcast logic

### Task 4.3: Client-to-Client Communication
- [ ] Setup individual channels untuk setiap client
- [ ] Implement client message sender thread
- [ ] Handle client disconnections gracefully
- [ ] Test message delivery

---

## Phase 5: Protocol Implementation

### Task 5.1: Message Protocol Design
- [ ] Define simple text protocol
  - [ ] `JOIN:<username>` - client join
  - [ ] `MSG:<message>` - broadcast message
  - [ ] `QUIT` - client disconnect
  - [ ] `LIST` - list online users
- [ ] Implement message parsing
- [ ] Handle malformed messages

### Task 5.2: Client State Management
- [ ] Track client usernames
- [ ] Implement username uniqueness check
- [ ] Handle client state transitions
- [ ] Implement timeout untuk inactive clients

### Task 5.3: Server Responses
- [ ] Implement server response messages
  - [ ] Welcome message
  - [ ] User joined/left notifications
  - [ ] Error messages
  - [ ] System announcements
- [ ] Format messages dengan timestamps

---

## Phase 6: Advanced Features

### Task 6.1: Command System
- [ ] Implement `/help` command
- [ ] Add `/list` untuk show online users
- [ ] Add `/whisper <user> <message>` untuk private messages
- [ ] Add `/nick <newname>` untuk change username

### Task 6.2: Message History
- [ ] Store recent messages dalam memory
- [ ] Send message history ke new clients
- [ ] Implement message limit (e.g., last 50 messages)
- [ ] Add timestamps ke semua messages

### Task 6.3: Enhanced Error Handling
- [ ] Create custom error types
- [ ] Implement proper error propagation
- [ ] Add recovery mechanisms
- [ ] Log errors dengan context

---

## Phase 7: Performance dan Reliability

### Task 7.1: Resource Management
- [ ] Implement proper cleanup pada client disconnect
- [ ] Handle thread panics dengan `catch_unwind`
- [ ] Limit maximum concurrent connections
- [ ] Monitor resource usage

### Task 7.2: Message Rate Limiting
- [ ] Implement basic rate limiting per client
- [ ] Use token bucket atau sliding window
- [ ] Handle spam detection
- [ ] Add cooldown periods

### Task 7.3: Graceful Shutdown
- [ ] Handle `Ctrl+C` signal
- [ ] Implement server shutdown sequence
- [ ] Notify all clients sebelum shutdown
- [ ] Wait for threads to complete

---

## Phase 8: Testing dan Debugging

### Task 8.1: Unit Tests
- [ ] Test message parsing functions
- [ ] Test client registry operations
- [ ] Test message routing logic
- [ ] Test error handling scenarios

### Task 8.2: Integration Tests
- [ ] Test multiple client connections
- [ ] Test message broadcasting
- [ ] Test client disconnect scenarios
- [ ] Test server under load

### Task 8.3: Simple Client untuk Testing
- [ ] Buat simple client application
- [ ] Implement interactive chat interface
- [ ] Add command line arguments
- [ ] Test berbagai scenarios

---

## Phase 9: Documentation dan Cleanup

### Task 9.1: Code Documentation
- [ ] Add rustdoc comments ke public APIs
- [ ] Document message protocol
- [ ] Create usage examples
- [ ] Document architecture decisions

### Task 9.2: Code Refactoring
- [ ] Split code ke multiple modules
- [ ] Improve error messages
- [ ] Optimize performance bottlenecks
- [ ] Clean up unused code

### Task 9.3: Deployment Preparation
- [ ] Add configuration file support
- [ ] Implement logging levels
- [ ] Add metrics collection
- [ ] Create deployment scripts

---

## Key Learning Points per Phase

### Concurrency Concepts:
- **Phase 3**: Thread spawning, ownership transfer
- **Phase 4**: `Arc<Mutex<>>`, shared state management
- **Phase 4**: `mpsc channels`, message passing
- **Phase 7**: Thread synchronization, graceful shutdown

### Rust-Specific:
- **Phase 2**: Error handling dengan `Result`
- **Phase 3**: Move semantics dalam threads
- **Phase 4**: Smart pointers (`Arc`, `Mutex`)
- **Phase 6**: Pattern matching dengan enums

### Networking:
- **Phase 2**: TCP basics, `TcpListener`, `TcpStream`
- **Phase 5**: Protocol design
- **Phase 7**: Connection management
- **Phase 8**: Load testing

---

## Bonus Challenges (Optional)

### Advanced Concurrency
- [ ] Replace `std::thread` dengan `tokio` async runtime
- [ ] Implement custom thread pool
- [ ] Add work-stealing queue
- [ ] Compare performance metrics

### Enhanced Features
- [ ] Add chat rooms/channels
- [ ] Implement file transfer
- [ ] Add encryption (TLS)
- [ ] Create web interface dengan WebSockets

### Production Readiness
- [ ] Add persistent message storage
- [ ] Implement user authentication
- [ ] Add admin commands
- [ ] Create monitoring dashboard


1. cargo new chat_server