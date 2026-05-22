# Email AI Assistant — Project Specification

## Overview

An AI assistant that automates email workflows — reading, drafting, and routing emails via Microsoft Graph API. Built for Diego Prista (DMC Production-NL) to integrate with his Outlook/Microsoft 365 account.

## Goals

- Read emails from Outlook inbox (Microsoft Graph API)
- Draft replies and new emails based on context/instructions
- Route emails to appropriate folders or flag for follow-up
- Surface actionable email summaries on demand

## Architecture

- **Runtime**: Python (local Macbook Air, OpenCode)
- **Email API**: Microsoft Graph API (delegated flow, OAuth2)
- **AI**: Claude via Anthropic API (or local model)
- **Sync**: GitHub repo (`diegoPrista/email-ai-assistant`) for version control and collaboration

## Authentication

- Azure AD app registration (client ID: `1506ba02-1f49-4b2e-8fc3-ff090df98603`)
- Delegated flow — user signs in interactively once
- Required permissions: `Mail.Read`, `Mail.ReadWrite`
- Token storage: local file (OAuth refresh token)

## Scope — Phase 1 (MVP)

1. List recent emails from inbox (last 20, unread first)
2. Read full email body (plain text + HTML)
3. Draft a reply given email content + instruction
4. Mark email as read/unread, move to folder

## Out of Scope (Phase 1)

- Sending email (Phase 2)
- Email search/filter beyond recent inbox
- Calendar integration
- Attachment handling

## Tech Stack

- Python 3.11+
- `msal` — Microsoft Authentication Library
- `anthropic` — Claude API client
- `readchar` — terminal keyboard input (interactive mode)
- `httpx` — HTTP client for Graph API

## Workflow

1. User runs script on Mac Mini: `python email_assist.py`
2. Script lists recent unread emails
3. User selects email by number
4. User gives instruction (e.g. "draft a reply thanking them")
5. Script calls Claude → returns draft
6. User approves/edits → script marks email read/sends

## File Structure

```
email-ai-assistant/
├── SPEC.md              # This file
├── README.md            # Project overview
├── email_assist.py       # Main entry point
├── auth/
│   └── token_manager.py  # OAuth token lifecycle
├── graph/
│   └── client.py        # Microsoft Graph API wrapper
├── prompts/
│   └── draft_reply.md   # Claude prompt template
└── requirements.txt
```

## Open Questions

- [ ] IT approval: Azure app admin consent for Mail.Read/Mail.ReadWrite
- [ ] Claude API key — use existing or create new?
- [ ] Interactive CLI vs API server mode?

## References

- Microsoft Graph: https://developer.microsoft.com/en-us/graph
- Azure app registration: https://portal.azure.com → App registrations
- Email skill: `/data/skills/productivity/email/SKILL.md`