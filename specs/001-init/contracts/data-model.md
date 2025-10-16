# Data Model & Protocol Specification

## Database Schema

```sql
-- Audio Files Table
CREATE TABLE audio_files (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    filename TEXT NOT NULL,
    duration_ms BIGINT NOT NULL,
    format TEXT NOT NULL,
    size_bytes BIGINT NOT NULL,
    waveform_data BYTEA,
    status TEXT NOT NULL DEFAULT 'pending',
    whisper_config JSONB,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    
    CONSTRAINT valid_status CHECK (status IN ('pending', 'processing', 'ready', 'error'))
);

-- Subtitles Table
CREATE TABLE subtitles (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    file_id UUID REFERENCES audio_files(id) ON DELETE CASCADE,
    text TEXT NOT NULL,
    start_time_ms BIGINT NOT NULL,
    end_time_ms BIGINT NOT NULL,
    confidence FLOAT,
    language TEXT,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    
    CONSTRAINT valid_time_range CHECK (start_time_ms < end_time_ms)
);

-- Indexes
CREATE INDEX idx_subtitles_file_id ON subtitles(file_id);
CREATE INDEX idx_subtitles_time_range ON subtitles(start_time_ms, end_time_ms);
```

## WebSocket Protocol

```typescript
interface WebSocketMessage {
  type: MessageType;
  payload: any;
  id: string;
  timestamp: number;
}

enum MessageType {
  // File Operations
  FILE_UPLOAD_START = 'file:upload:start',
  FILE_CHUNK = 'file:chunk',
  FILE_UPLOAD_COMPLETE = 'file:upload:complete',
  
  // Whisper Operations
  WHISPER_START = 'whisper:start',
  WHISPER_PROGRESS = 'whisper:progress',
  WHISPER_COMPLETE = 'whisper:complete',
  
  // Subtitle Operations
  SUBTITLE_UPDATE = 'subtitle:update',
  SUBTITLE_DELETE = 'subtitle:delete',
  
  // Timeline Operations
  TIMELINE_SEEK = 'timeline:seek',
  WAVEFORM_REQUEST = 'waveform:request',
  
  // System Operations
  ERROR = 'system:error',
  ACK = 'system:ack'
}
```