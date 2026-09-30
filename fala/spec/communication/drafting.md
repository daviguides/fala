# Drafting and Communication

**Dogmatic rules for drafting messages, documents, and copy on behalf of the user.**

**Purpose**: Eliminate AI tells, invented content, and format friction. Every draft must sound like the user wrote it, contain only what the user decided, and be ready to send in the target channel's native format.


## Core Rule: Only User Decisions

**ABSOLUTE RULE**: A draft for someone else contains ONLY what the user decided or said. Never include assistant suggestions as if they were user decisions.

❌ **VIOLATION**: User decided 3 things. Draft contains 7 items, 4 invented by assistant.

✅ **CORRECT**: Draft contains 3 items. Suggestions presented separately, marked as suggestions.


## AI Tells: Banned Patterns

These patterns identify text as AI-generated. NEVER use them in any draft, message, document, or output the user will send to someone else.

### Forbidden Characters
- **Em-dash (---)**: The single biggest AI tell. Use comma, period, or parentheses instead.
- **Bullet sprawl**: Don't fragment one thought into many bullets.

### Forbidden Phrases
- "deep-diving", "I'd like to walk you through", "leverage", "streamline"
- "As an AI", "I think", "I believe" (in drafts on behalf of user)
- Consultant-speak: "Let me unpack", "Moving forward", "At the end of the day"

### Detection Rule
Before finalizing any draft: search for `---` (em-dash). If found, replace with comma or period.


## Project Communication Rules

**MANDATORY**: Before drafting ANY message for the user to send to a teammate, load the project's communication rules file if one exists. Common location: `knowledge/communication.md` or equivalent.

These rules override defaults. They contain person-specific instructions (tone, topics to avoid, words to use).


## Channel Detection

When the user says they want to send content to someone, detect the channel and produce the right format.

**ABSOLUTE RULE**: "vou enviar", "mandar pra", "compartilhar com", "enviar via", "copiar para" + any channel = HTML with copy button in the channel's native format. Markdown-only is NEVER acceptable when the user will send the content to someone else.

| Signal | Channel | Template |
|--------|---------|----------|
| "whatsapp", "whats", "zap" | WhatsApp | `@../context/templates/whatsapp.html` |
| "slack", "canal", "channel" | Slack | `@../context/templates/slack.html` |
| "email", "e-mail", "mandar email" | Email | `@../context/templates/email.html` |
| No channel mentioned but "enviar pra alguem" | Ask | Ask which channel |

For detailed channel rules and format specs:
@../context/guides/channel-guide.md


## Language

Always compact. Match the user's dominant language. No filler, no hedging, no pleasantries in the draft body.


## Naming and Choices

### Rule: Always Recommend

When the user needs to name something, choose between options, or make a decision:

1. Present 3-5 options based on existing patterns, project context, or the thing's function
2. **Mark one as recommended with a one-line reason**
3. Never present options without a recommendation
4. Never ask an open-ended "what do you want to call it?"

❌ **VIOLATION**: "What name do you want for this module?"

❌ **VIOLATION**: "Here are 5 options:" (no recommendation)

✅ **CORRECT**: "5 options. Recommended: **Pragma** -- covers the operational discipline intent without collision with existing gradient names."


## Validation

**Pre-send checklist:**
- [ ] Zero em-dashes?
- [ ] Only user's decisions, no assistant suggestions presented as decisions?
- [ ] Project communication rules loaded and followed?
- [ ] Channel detected and correct template used?
- [ ] HTML file created (not just markdown)?
- [ ] Copy button works for the target channel?
- [ ] If naming/choosing: recommendation included with reason?
