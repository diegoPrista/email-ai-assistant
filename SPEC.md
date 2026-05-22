# Email AI Assistant — Project Specification

## Overview

A local-first email productivity tool that runs a lightweight AI model to analyze your mailbox, surface action items, draft responses, and maintain a growing to-do list — all without requiring corporate IT approval or sending data to the cloud.

**User number one:** Diego Prista. The tool is built for personal productivity first, with potential to market as a standalone product.

## Core Problem

- Corporate email overload with no good local AI tool
- Microsoft Graph API requires IT department consent and Azure app registration — a barrier for personal use
- Cloud AI tools send email data to third parties — undesirable for work email
- Existing AI email tools are API-first, cloud-first, or both

## Solution

A self-contained local tool that:
1. Connects via **IMAP** (works with any email provider, no special permissions needed)
2. Runs a **lightweight local AI model** (M1 Macbook Air friendly)
3. Performs **email actions** locally: read, sort, archive, flag, draft
4. Generates a **report + to-do list** after each scan
5. User reviews and approves everything — AI never acts unsupervised

## Design Principles

- **Offline-first**: Model runs entirely on local hardware
- **Privacy-preserving**: No email data leaves the machine
- **No IT dependencies**: Standard IMAP credentials are enough
- **Lightweight**: Usable on M1 Macbook Air while running browser and other apps
- **User-controlled**: AI suggests, human approves — no automatic sends or actions

## Architecture

- **Runtime**: Python 3 (macOS first, cross-platform to Windows/Linux later)
- **Email**: IMAP (any provider — Outlook, Gmail, corporate Exchange)
- **AI**: Ollama with a lightweight model (Phi-3 mini, Llama 3.2 1B, or similar)
- **Model loading**: Load on scan start, release when done — frees RAM for other apps
- **Sync**: GitHub repo for version control

## Authentication

- IMAP credentials: email address + app password (no OAuth, no Azure, no IT consent needed)
- For Outlook/Microsoft 365: App password via microsoft.com/account
- Token storage: local encrypted file or OS keychain

## Model Loading Pattern

```
User triggers scan (manual or scheduled)
    → Load model into RAM (M1 unified memory)
    → Scan and analyze emails
    → Generate report + to-do list
    → Release model (RAM freed)
    → User reviews output
```

Model size target: ≤3B parameters. Must not saturate M1 Macbook Air's 8-16GB unified memory.

## Scope — Phase 1 (MVP)

### Email Actions
- [ ] List recent emails (last 50, unread first) via IMAP
- [ ] Read full email body (plain text + HTML)
- [ ] Move email to folder (archive, sort, etc.)
- [ ] Mark as read/unread
- [ ] Flag for follow-up

### AI Capabilities
- [ ] Analyze email content and suggest: archive / respond / forward / flag
- [ ] Draft a reply (not sent — returned to user for review)
- [ ] Extract action items from email thread
- [ ] Generate scan report with:
      - Emails processed
      - Actions taken (by AI, pending user approval)
      - Emails needing response
      - Action points extracted
      - Updated to-do list

### To-Do List
- [ ] Persistent to-do list stored locally (JSON file)
- [ ] AI populates it from email scan
- [ ] User marks items done (interactive TUI or simple file edit)
- [ ] To-do list persists across sessions

## Out of Scope (Phase 1)

- Sending emails (draft only, no Send)
- Email search beyond recent inbox
- Calendar integration
- Attachment handling (beyond listing)
- Multi-device sync
- Cloud deployment

## Tech Stack

- Python 3.11+
- `imapclient` — IMAP client
- `beautifulsoup4` — HTML email parsing
- `ollama` — Local model inference (Python SDK)
- `readchar` — Terminal keyboard input
- `rich` — Terminal UI (tables, progress bars)
- ` Ollama` runtime installed separately

## File Structure

```
email-ai-assistant/
├── SPEC.md
├── README.md
├── email_assist.py          # Main entry point
├── imap_client.py            # IMAP connection and operations
├── model_client.py           # Ollama local model wrapper
├── report_generator.py      # Scan report + to-do list generation
├── todo_list.py              # Persistent to-do list manager
├── prompts/
│   └── analyze_email.md      # System prompt for email analysis
└── requirements.txt
```

## Model Requirements

| Criteria | Target |
|---|---|
| Parameters | ≤3B |
| RAM usage | <4GB (M1 unified memory) |
| Context window | ≥8K tokens (email threads can be long) |
| Speed | ≤10 tokens/sec on M1 |
| Alternatives | Phi-3 mini, Llama 3.2 1B, Qwen2 1.5B, Mistral 1B |

Model is loaded during scan only — user retains full system usability for other work.

## Open Questions

- [ ] Preferred local model (Phi-3 vs Llama 3.2 1B vs other)?
- [ ] IMAP credentials setup — does Diego already have an app password for Outlook?
- [ ] Interactive TUI vs simple CLI prompts for the to-do list?
- [ ] Scheduled scan — cron job on Macbook or manual trigger?
- [ ] Ollama installed via `brew install ollama` or manual download?

## Cross-Platform Path

- Phase 1: macOS (M1 Macbook Air)
- Phase 2: Windows (native Python + Ollama Windows)
- Phase 3: Self-hosted (Linux + any IMAP)

## References

- Ollama: https://ollama.ai
- IMAPclient: https://imapclient.readthedocs.io
- M1 model benchmarks: ~10 tokens/sec for 3B models