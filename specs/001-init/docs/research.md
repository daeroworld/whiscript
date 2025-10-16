# Technical Research & Decisions

## Architecture Decisions

### 1. Backend Stack
**Decision**: Go + PostgreSQL + Whisper + WebSocket
**Rationale**: 
- Go provides excellent concurrency for WebSocket connections
- PostgreSQL offers robust JSONB type for subtitle data
- Direct Whisper CLI/API integration from Go
- WebSocket enables real-time subtitle updates

**Alternatives Considered**:
- gRPC: Rejected due to added complexity with browser clients
- REST API: Rejected due to polling overhead
- Server-Sent Events: Rejected due to one-way communication

### 2. Speech Recognition
**Decision**: Whisper CLI/API with Go wrapper
**Rationale**:
- High accuracy transcription
- Language detection built-in
- Timestamp generation included
- Go can efficiently manage CLI processes

**Alternatives Considered**:
- Cloud speech services: Higher cost, vendor lock-in
- Browser Web Speech API: Lower accuracy
- Custom speech recognition: Unnecessary complexity

### 3. WebSocket Protocol
**Decision**: Custom JSON-based protocol
**Rationale**:
- Lightweight message format
- Easy debugging with JSON
- Supports all required operations
- Built-in ping/pong for connection health

**Message Types**:
- File operations (upload, process)
- Subtitle operations (create, update, delete)
- Timeline operations (sync, seek)
- System operations (error, ack)

### 4. Performance Optimizations
**Decision**: Chunked Processing + Streaming
**Rationale**:
- Handle large files efficiently
- Stream partial results to client
- Progressive waveform generation
- Memory-efficient processing