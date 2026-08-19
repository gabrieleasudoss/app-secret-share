# Secret Share

Self-destructing secret sharing — share passwords, API keys, and tokens via encrypted one-time links.

## How It Works

```
You → Create secret (encrypted client-side) → Get shareable link
Recipient → Opens link → Sees secret → Link self-destructs forever
```

## Features

- AES-256 encryption (key never stored on server)
- One-time view — auto-deletes after first read
- Expiration: 1 hour, 24 hours, 7 days, or custom
- Optional password protection
- No account required
- Audit log: viewed timestamp (no content logged)
- API for programmatic use

## Tech Stack

| Layer | Technology |
|---|---|
| **Backend** | Go |
| **Encryption** | AES-256-GCM (client-side) |
| **Storage** | Redis (TTL-based auto-expiry) |
| **Frontend** | React, TypeScript |
| **Containerization** | Docker, Docker Compose |

## Architecture

```
┌──────────────┐     ┌─────────────────────────┐     ┌───────┐
│  Browser     │────▶│  Go Server              │────▶│ Redis │
│  (encrypts)  │◀────│  ├── /api/secrets POST  │◀────│ (TTL) │
└──────────────┘     │  ├── /api/secrets/:id   │     └───────┘
                     │  └── /api/health        │
                     └─────────────────────────┘

Encryption happens CLIENT-SIDE:
1. Browser generates random AES key
2. Encrypts secret with key
3. Sends only ciphertext to server
4. Key is embedded in URL fragment (#) — never sent to server
5. Recipient's browser decrypts using the key from URL
```

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/secrets` | Store encrypted secret |
| GET | `/api/secrets/:id` | Retrieve & destroy secret (one-time) |
| GET | `/api/secrets/:id/exists` | Check if secret still exists |
| GET | `/api/health` | Health check |

## Security Model

- Server **never sees plaintext** — only ciphertext
- Encryption key lives in URL fragment (`#key`) — not sent to server
- Redis TTL ensures secrets auto-expire even if never viewed
- No logs of secret content
- Rate limiting prevents brute-force

## Getting Started

```bash
docker-compose up
# App at http://localhost:3000
# API at http://localhost:8080
```

## Roadmap

- [x] Project setup
- [ ] Go backend with Redis storage
- [ ] Client-side AES-256 encryption
- [ ] One-time retrieval logic
- [ ] TTL-based expiration
- [ ] React frontend
- [ ] Password protection option
- [ ] Docker deployment
- [ ] Rate limiting

## License

MIT
