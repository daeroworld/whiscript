# Quick Start Guide

## Prerequisites

- Go 1.25+
- Node.js 22+
- PostgreSQL 15+
- FFmpeg
- Whisper CLI/API credentials
- Docker & Docker Compose (optional)

## Development Setup

1. Clone the repository:
```bash
git clone <repository-url>
cd subtitle-editor
```

2. Install dependencies:
```bash
# Frontend
cd frontend
npm install

# Backend
cd ../backend
go mod download
```!

3. Set up environment variables:
```bash
# Frontend (.env.local)
NEXT_PUBLIC_API_URL=http://localhost:8080
NEXT_PUBLIC_MAX_FILE_SIZE=250000000

# Backend (.env)
DATABASE_URL=postgresql://user:pass@localhost:5432/subtitles
PORT=8080
```

4. Start development servers:
```bash
# Frontend
cd frontend
npm run dev

# Backend
cd ../backend
go run cmd/server/main.go
```

Visit http://localhost:3000 to start development.