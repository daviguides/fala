# Communication Failures (Real Examples)

Few-shot examples of drafting and communication mistakes.

For the rules these illustrate: @~/.claude/fala/spec/communication/drafting.md


## Failure 1: Em-dash in draft

**What happened**: Draft for Kyle contained "an in-platform assistant -- brands first". Kyle's team would recognize AI-generated text.

**Root cause**: Em-dash (---) is the most recognizable AI writing pattern.

❌ "We build the Trybe Assistant -- an in-platform assistant for brands."
✅ "We build the Trybe Assistant, an in-platform assistant for brands."


## Failure 2: Assistant suggestions as user decisions

**What happened**: One-pager for Kyle contained 7 items. Only 3 were user decisions. The other 4 were assistant suggestions written as commitments.

**Root cause**: Draft mixed decided items with proposed items without marking them.

❌ "Wave 1: read only, no actions yet" (assistant suggestion, unmarked)
✅ Separate: "Wave 1 (decided): [3 items]" then "Suggestions (not in draft): [4 items]"


## Failure 3: Markdown in Slack copy zone

**What happened**: HTML file had a copy zone with `*bold*` and `- bullet` markdown. Pasted into Slack as literal asterisks and dashes.

**Root cause**: Slack doesn't parse pasted markdown. Needs rendered HTML for rich text.

❌ `<div class="slack-copy">*Trybe Assistant*\n- item 1</div>`
✅ `<div class="copy-zone"><b>Trybe Assistant</b><br><br><ul><li>item 1</li></ul></div>`


## Failure 4: Sections collapsed in Slack

**What happened**: HTML used `<p>` tags between sections. Pasted into Slack with no spacing between sections.

**Root cause**: Slack ignores CSS margins on pasted content.

❌ `<p>Section 1</p><p>Section 2</p>`
✅ `Section 1<br><br>Section 2`


## Failure 5: Asked for name without options

**What happened**: User needed a name for a gradient plugin. Assistant asked "what do you want to call it?"

**Root cause**: Open-ended question when options derivable from context.

❌ "What name do you want for this plugin?"
✅ "5 options based on existing gradient naming (Japanese/Greek terms). Recommended: **Pragma** -- operational discipline, no collision with existing names."


## Failure 6: Options without recommendation

**What happened**: Presented 5 naming options. No recommendation marked.

**Root cause**: Gave options but forced user to evaluate all 5 equally.

❌ "Here are 5 options: A, B, C, D, E"
✅ "Recommended: **C** because [reason]. Alternatives: A, B, D, E."


## Failure 7: Offering memory after rejection

**What happened**: User explained memory doesn't work (other assistants, no direct access). Three turns later, assistant offered to save to memory again.

**Root cause**: Correction not held as permanent constraint.

❌ "Want me to save this to memory?"
✅ Update project knowledge base. Never mention memory again.


## Failure 8: "Rendered Preview" label

**What happened**: HTML had a label "Rendered Preview" above the content. Meaningless to the user.

**Root cause**: Meta-labels describing the UI instead of just showing the content.

❌ `<div class="section-title">Rendered Preview</div>`
✅ Just show the content. No labels about the page itself.


## Failure 9: Using `open` when OS default changed

**What happened**: Ran `open file.md` to show the user a file. macOS had silently reassigned `.md` to Claude.app (desktop), so the file opened in the wrong application.

**Root cause**: `open` delegates to the OS default handler. App updates or reinstalls can change it without warning.

❌ `open /path/to/file.md`
✅ `zed /path/to/file.md` (explicit editor command)

Use `open` only for HTML files intended for the browser. For code, markdown, config: use the editor the user works in.


## Failure 10: Expanding scope when user is trying to close

**What happened**: User was narrowing down a one-pager for Kyle. Every question the user asked, the assistant opened a new thread instead of converging. User: "vc nao vai em direcao a algo, sempre abre abre abre e nunca chega a nada."

**Root cause**: Treating each user question as an invitation to explore, instead of recognizing the user is closing scope.

❌ User asks about handoff mechanism. Assistant opens: Crisp integration, email notification, Slack alerts, desk UI, provider column.
✅ User asks about handoff mechanism. Assistant answers the question, then returns to the deliverable.


## Failure 11: Markdown file when user said "via WhatsApp"

**What happened**: User said "salve em um markdown, eu vou enviar via whats". Assistant saved a .md file only. User had to explicitly ask for HTML template.

**Root cause**: Communication guide was titled "Slack HTML guide", so the assistant treated HTML-with-copy-button as Slack-specific instead of channel-generic. "Enviar via WhatsApp" should have triggered the WhatsApp template immediately.

❌ Save markdown only when user says "via WhatsApp"
✅ Detect "via WhatsApp" as channel signal, generate HTML with WhatsApp markdown textarea + copy button. Keep the .md too if requested, but HTML is mandatory.

**Key lesson**: any "vou enviar para alguem" signal triggers HTML with copy button, regardless of channel. The guide is channel-generic, not Slack-specific.
