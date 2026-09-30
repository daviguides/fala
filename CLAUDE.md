# Fala - Claude Code Project Instructions

## Project Overview

**Fala** provides multi-channel communication drafting for Claude Code.

**Purpose**: Plugin that handles drafting messages, one-pagers, and any content the user will send to someone else. Detects the target channel (WhatsApp, Slack, email) and produces a local HTML file with complete template, copy button, and text in the channel's native format.

---

## Structure

```
fala/
├── fala/                     # BUNDLE (installed to ~/.claude/fala)
│   ├── spec/
│   │   └── communication/    # Drafting rules, AI tells, channel detection
│   ├── context/
│   │   ├── guides/           # Channel-specific format rules
│   │   ├── examples/         # Real communication failures
│   │   └── templates/        # Complete HTML templates per channel
│   └── prompts/
├── skills/
│   └── load/                 # Load communication principles
├── commands/
│   └── load.md
└── .claude-plugin/
    └── plugin.json
```

---

## Channels

| Channel | Template | Copy Method | Format |
|---------|----------|-------------|--------|
| WhatsApp | `templates/whatsapp.html` | Plain text from hidden textarea | `*bold*`, `•` bullets, newlines |
| Slack | `templates/slack.html` | Rich text from rendered HTML | `<b>`, `<br><br>`, `<ul><li>` |
| Email | `templates/email.html` | HTML with inline styles | `<table>`, inline `style="..."` |

---

## Skills

| Skill | Trigger | Purpose |
|-------|---------|---------|
| `/fala:load` | "load communication" | Load all drafting specs and templates |

---

## Relationship with Other Plugins

- **Pragma**: Fala extracts the communication/drafting responsibility from Pragma. Pragma keeps delegation, persistence, and anti-waste.
- **Arche**: Behavioral principles (mode detection). Fala handles communication mechanics, not modes.

---

## Core Principle

Every draft sounds like the user wrote it. No AI tells, no invented decisions, right format for the right channel.

---

## Development Notes

- **Size constraint**: Keep total under 50KB
- **No em-dashes**: Use commas, periods, or parentheses
- **Evidence-based**: Every rule traces to a real session failure
- **Templates**: Must be COMPLETE, working HTML files, not code snippets
