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
    <h1>, <h2>, <p>, <ul><li>, <b> — normal HTML for visual preview
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
- Rich text copy (getSelection + execCommand) — WhatsApp strips it
- `<p>` tags in the textarea — use plain newlines
- Markdown `**bold**` — WhatsApp uses single `*bold*`
- `-` for bullets — use `•`

### Reference Template
`@../templates/whatsapp.html`


## Slack

### Detection Signals
"slack", "canal", "channel", "mandar no slack"

### Format Rules
- Slack receives rich text from clipboard when copying rendered HTML.
- Use `<b>` for bold, `<ul><li>` for bullets.
- Use `<br><br>` between sections — Slack eats CSS margins.
- NO `<p>` tags — Slack collapses margins, sections run together.
- NO markdown in the copy zone — `*bold*` pastes as literal asterisks.

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
  getSelection() + execCommand('copy') — preserves rich text
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
- `navigator.clipboard.writeText()` — loses rich text, pastes as plain
- `navigator.clipboard.write()` with text/html — inconsistent line breaks
- `<p>` tags — Slack collapses margins
- Markdown in the copy zone — pasted literally
- Nested `<div>` structure — use flat HTML

### Reference Template
`@../templates/slack.html`


## Email

### Detection Signals
"email", "e-mail", "mandar email", "enviar por email"

### Format Rules
- Email clients (Gmail, Outlook) drop `<style>` blocks. ALL styles must be inline (`style="..."`).
- Use `<table>`, `<tr>`, `<td>` for layout — email clients break flexbox and grid.
- Use `<span style="font-weight:600">` for bold, not `<b>` (more reliable across clients).
- Font stack: `-apple-system,BlinkMacSystemFont,"Segoe UI",Helvetica,Arial,sans-serif`
- Colors as `rgb()` values, not hex — more reliable in email clients.

### HTML Structure
Two views: compose preview (what the user sees) + raw body (what gets copied).

```
Compose preview:
  <div class="compose-window">
    From: / To: / Subject: fields
    <div class="compose-body" id="email-body">
      [email body with inline styles]
    </div>
  </div>

Copy button:
  navigator.clipboard.write() with text/html + text/plain
  fallback: getSelection + execCommand
```

### Copy Mechanism
```javascript
document.getElementById('copy-body').addEventListener('click', async function(event) {
  var body = document.getElementById('email-body');
  var html = body.innerHTML;
  try {
    await navigator.clipboard.write([new ClipboardItem({
      'text/html': new Blob([html], {type: 'text/html'}),
      'text/plain': new Blob([body.innerText], {type: 'text/plain'}),
    })]);
    // success
  } catch (error) {
    var range = document.createRange();
    range.selectNodeContents(body);
    var selection = window.getSelection();
    selection.removeAllRanges();
    selection.addRange(range);
    // user copies manually
  }
});
```

### Forbidden
- `<style>` blocks — email clients drop them
- Tailwind or external CSS — email clients can't load them
- Flexbox or Grid — email clients break them
- Hex colors — some clients mishandle them
- `<b>` without inline style — unreliable in some clients

### Reference Template
`@../templates/email.html`


## Common Rules (All Channels)

1. **No em-dashes** anywhere in any file.
2. **No meta-labels** like "Rendered Preview" or "Copy Zone" visible to the user.
3. **No recipient name in title** — "One-Pager for Kyle" is wrong, user knows who it's for.
4. **Dark mode support** in all templates.
5. **Button text in user's language** — "Copiar para WhatsApp", not "Copy for WhatsApp" (when user speaks Portuguese).
6. **Always save as local HTML** and open with `open <path>`. Never use the Artifact tool.
