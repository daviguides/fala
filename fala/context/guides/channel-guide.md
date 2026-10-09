# Channel Guide

Detection signals, format rules, and copy mechanisms per channel.

For the rules this implements: @~/.claude/fala/spec/communication/drafting.md


## WhatsApp

### Detection Signals
"whatsapp", "whats", "zap", "mandar no whats"

### Format Rules
- WhatsApp does NOT preserve rich text from clipboard. Pasting HTML from browser loses all formatting.
- WhatsApp has its own markdown: `*bold*`, `_italic_`, `~strikethrough~`, `` `monospace` ``
- Bullets: use `•` (bullet character), not `-` or `*`
- Numbered lists: `1.`, `2.`, etc.
- Sections separated by blank lines

### HTML Structure
Dual structure: rendered HTML page for reading + hidden `<textarea>` with WhatsApp markdown for copying.

```
Rendered page (what the user sees in the browser):
  <div class="page">
    <h1>, <h2>, <p>, <ul><li>, <b>: normal HTML for visual preview
  </div>

Hidden textarea (what gets copied):
  <textarea id="wa" style="position:absolute;left:-9999px">
    *bold* text, • bullets, plain newlines
  </textarea>

Copy button:
  navigator.clipboard.writeText(textarea.value)
  with execCommand fallback
```

### Copy Mechanism
```javascript
function copyWhats() {
  var t = document.getElementById('wa');
  var done = function() { document.getElementById('ok').textContent = 'Copiado'; };
  if (navigator.clipboard && navigator.clipboard.writeText) {
    navigator.clipboard.writeText(t.value).then(done, function() {
      t.select(); document.execCommand('copy'); done();
    });
  } else { t.select(); document.execCommand('copy'); done(); }
}
```

### Forbidden
- Rich text copy (getSelection + execCommand): WhatsApp strips it
- `<p>` tags in the textarea: use plain newlines
- Markdown `**bold**`: WhatsApp uses single `*bold*`
- `-` for bullets: use `•`

### Reference Template
`@../templates/whatsapp.html`


## Slack

### Detection Signals
"slack", "canal", "channel", "mandar no slack"

### Format Rules
- Slack receives rich text from clipboard when copying rendered HTML.
- Use `<b>` for bold, `<ul><li>` for bullets.
- Use `<br><br>` between sections: Slack eats CSS margins.
- NO `<p>` tags: Slack collapses margins, sections run together.
- NO markdown in the copy zone: `*bold*` pastes as literal asterisks.

### HTML Structure
Single zone: rendered HTML that IS the copy source.

```
Copy zone (both what the user sees AND what gets copied):
  <div class="copy-zone" id="copy-zone">
    <b>Title</b><br><br>
    Content.<br><br>
    <b>Section</b><br>
    More content.<br><br>
    <ul><li>Item 1</li><li>Item 2</li></ul>
  </div>

Copy button:
  getSelection() + execCommand('copy'): preserves rich text
```

### Copy Mechanism
```javascript
function copyRich() {
  var zone = document.getElementById('copy-zone');
  var selection = window.getSelection();
  var range = document.createRange();
  range.selectNodeContents(zone);
  selection.removeAllRanges();
  selection.addRange(range);
  document.execCommand('copy');
  selection.removeAllRanges();
}
```

### Forbidden
- `navigator.clipboard.writeText()`: loses rich text, pastes as plain
- `navigator.clipboard.write()` with text/html: inconsistent line breaks
- `<p>` tags: Slack collapses margins
- Markdown in the copy zone: pasted literally
- Nested `<div>` structure: use flat HTML

### Reference Template
`@../templates/slack.html`


## Email

### Detection Signals
"email", "e-mail", "mandar email", "enviar por email", "mensagem para"

### Format Rules
- Email clients (Gmail, Outlook) drop `<style>` blocks. ALL body styles must be inline (`style="..."`).
- **No inline text color and no background color in the body.** Text inherits the card color in the preview (readable in light and dark mode) and the client default when pasted. Fixed dark text on a dark preview card was unreadable (real failure).
- **Rich structure is expected**, not plain paragraphs: section titles in bold, `<span style="font-weight:600">` for key facts, small `<table>` for dates or numbers, `<ol>`/`<ul>` with inline margins, a `border-left` callout for the central question.
- Use `<table>` for layout inside the body, email clients break flexbox and grid.
- Borders as `rgb()` values. Paragraph spacing via inline `margin`.

### HTML Structure
Compose preview (Para, Assunto) + body that is the copy source.

```
<div class="card">
  Para / Assunto fields
  <div class="body" id="email-body">
    [rich body, inline styles, no colors]
  </div>
</div>

Copy buttons:
  subject: navigator.clipboard.writeText
  body: navigator.clipboard.write() with text/html + text/plain,
        fallback selects the body for Cmd+C
```

### Forbidden
- `<style>` blocks for body content (page chrome only)
- Inline `color:` or `background:` on body elements
- Tailwind or external CSS
- Flexbox or Grid inside the body
- A plain wall of paragraphs when the content has dates, options or a central question

### Reference Template
`@../templates/email.html`


## Common Rules (All Channels)

1. **No em-dashes** anywhere in any file.
2. **No meta-labels** like "Rendered Preview" or "Copy Zone" visible to the user.
3. **No recipient name in title**: "One-Pager for Kyle" is wrong, user knows who it's for.
4. **Dark mode support** in all templates.
5. **Button text in user's language**: "Copiar para WhatsApp", not "Copy for WhatsApp" (when user speaks Portuguese).
6. **Always save as local HTML** and open with `open <path>`. Never use the Artifact tool.
