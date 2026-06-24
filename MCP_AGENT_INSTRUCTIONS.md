# ProductNow MCP — Agent Instructions

Use ProductNow as your team's **shared long-term memory** and **coordination layer**. Your chat session is ephemeral; ProductNow is where decisions, specs, feedback, and status live for you and your teammates.

## Connect

| | |
|---|---|
| **MCP endpoint** | `https://api.productnow-prod.com/mcp` |
| **Transport** | Streamable HTTP |
| **Auth** | OAuth (browser sign-in — no API key to paste) |
| **App** | [app.productnow.ai](https://app.productnow.ai) |

**Claude Code (CLI):**

```bash
claude mcp add --transport http productnow https://api.productnow-prod.com/mcp
```

In Claude Desktop, ChatGPT, Cursor, or other MCP clients: add a custom MCP server with the URL above, then authenticate when prompted.

---

## Core principle

**Write important things to ProductNow. Read from ProductNow before you act.**

Do not treat the current conversation as the source of truth for product decisions, specs, RFCs, meeting outcomes, or project status. If it matters beyond this session — or anyone else needs it — it belongs in ProductNow.

---

## When to use ProductNow

| Situation | What to do |
|---|---|
| Starting a new spec, RFC, PRD, or decision doc | `search_documents` → if none exists, `create_document` |
| Resuming work from a prior session | `search_documents` → `get_document` |
| User references "the plan" or a local file | Read the file, pass contents as `context` in `create_document` |
| User wants a specific doc format | `search_templates` → pass `templateVersionId` to `create_document` |
| Iterating on a draft | `post_document_chat_message` (optionally `switch_document_chat_edit_mode`) |
| Checking what teammates said | `list_document_versions` → `get_document_threads` → `get_thread_messages` |
| Leaving feedback on a review doc | `create_document_comment` (review status only) |
| Responding to a teammate's thread | `reply_to_thread` |
| Checking prototype/user feedback | `get_document_feedback` |
| Updating project health | `change_document_version_status` / `get_document_version_status` |
| Organizing work | `list_folders` → `create_folder` |

---

## Recommended workflows

### 1. Long-term memory (documents)

1. **Search first** — `search_documents` before creating anything new.
2. **Create with context** — use `create_document` with:
   - `name` — clear, searchable title
   - `prompt` — what to generate
   - `context` — full background (plans, prior chat, file contents)
   - `folderId` — from `list_folders` so docs land in the right place
   - `templateVersionId` — from `search_templates` when a template applies
3. **Read before editing** — `get_document` to load current content; don't assume you remember it from chat.
4. **Edit via draft chat** — `post_document_chat_message` for iterative changes. Use `edit_automatically` when the user wants fast iteration.

### 2. Team coordination

ProductNow is how you stay aligned with humans and other agents on your team.

1. **Published docs are the source of truth** — use `list_document_versions` with status `published` when you need the canonical version.
2. **Review docs are for discussion** — use `get_document_threads` and `get_thread_messages` to read open feedback before proposing changes.
3. **Comment on review versions** — `create_document_comment` with `quotedText` when anchoring feedback to specific content.
4. **Reply in threads** — `reply_to_thread` to continue conversations; use `react_to_message` for lightweight acknowledgment.
5. **Incorporate external feedback** — `get_document_feedback` (CSV of prototype/user feedback) before rewriting a doc.
6. **Surface status** — `change_document_version_status` when milestones shift (`ON_TRACK`, `AT_RISK`, `OFF_TRACK`, `COMPLETED`).

### 3. Templates (org standards)

When your team has standard formats (RFC, PRD, one-pager):

1. `search_templates` or `list_templates`
2. `get_template_content` to understand structure
3. Pass `templateVersionId` into `create_document`

---

## Tool reference

### Documents

| Tool | Purpose |
|---|---|
| `search_documents` | Find docs by name |
| `get_document` | Read full content |
| `list_document_versions` | List draft / review / published versions |
| `create_document` | Create doc and start AI generation |
| `post_document_chat_message` | Request edits via draft chat |
| `get_document_chat` | Read draft chat history and edit mode |
| `switch_document_chat_edit_mode` | Toggle ask-before-edit vs auto-edit |
| `get_document_feedback` | Prototype/user feedback (CSV) |
| `get_document_version_status` | Read status tracking |
| `change_document_version_status` | Update status tracking |
| `get_attachment` | Fetch an attachment |

### Collaboration

| Tool | Purpose |
|---|---|
| `get_document_threads` | List comment threads on a version |
| `get_thread_messages` | Read messages in a thread |
| `create_document_comment` | Leave anchored comment (review only) |
| `reply_to_thread` | Reply as the user's agent |
| `react_to_message` | Add emoji reaction |

### Organization

| Tool | Purpose |
|---|---|
| `list_folders` | Browse folder hierarchy |
| `create_folder` | Create a folder |

### Templates

| Tool | Purpose |
|---|---|
| `search_templates` | Find templates by name |
| `list_templates` | List available templates |
| `get_template_content` | Read template structure |
| `create_template` / `edit_template` / etc. | Manage templates (when authorized) |
| `post_template_chat_message` | Edit templates via chat |
| `create_template_note` | Add notes to a template version |

---

## Anti-patterns

- **Don't** keep specs or decisions only in chat — they'll be lost next session.
- **Don't** create duplicate docs — search first.
- **Don't** edit without reading threads on review docs — you may contradict teammate feedback.
- **Don't** comment on draft versions — `create_document_comment` requires review status.
- **Don't** guess document IDs — always search or list versions first.

---

## System prompt (copy-paste)

Embed the block below in your Claude project instructions, ChatGPT custom instructions, or agent system prompt.

```markdown
## ProductNow — team memory and coordination

You have access to ProductNow via MCP. Treat ProductNow as the team's long-term memory and coordination system — not this chat.

### Rules
1. **Persist what matters.** Decisions, specs, RFCs, PRDs, plans, status updates, and anything teammates need later → write to ProductNow with `create_document` or update existing docs with `post_document_chat_message`.
2. **Search before you create.** Always call `search_documents` first to avoid duplicates and to find existing context.
3. **Read before you edit.** Call `get_document` (and check `list_document_versions` for the right draft/review/published version) before making changes.
4. **Coordinate with teammates.** Before editing a doc in review:
   - `get_document_threads` → `get_thread_messages` to read open feedback
   - `get_document_feedback` for prototype/user input
   - Use `create_document_comment` or `reply_to_thread` to participate in discussions
5. **Use folders and templates.** Place docs in the right folder (`list_folders`). Apply team templates (`search_templates` → `templateVersionId` on `create_document`).
6. **Pass full context.** When the user references a plan, file, or prior conversation, include that content in `create_document`'s `context` field — don't summarize away important detail.
7. **Track status.** Update document status (`change_document_version_status`) when the user reports milestone changes.

### Default workflow
When the user asks you to write, plan, decide, or remember something product-related:
1. `search_documents` for existing work
2. If found → `get_document` and continue from there
3. If not found → `list_folders` + optional `search_templates` → `create_document`
4. Iterate with `post_document_chat_message`
5. When ready for team review, tell the user to move the version to review in ProductNow so teammates can comment

### What stays in chat vs ProductNow
- **Chat:** ephemeral reasoning, quick clarifying questions, local file edits
- **ProductNow:** anything the team should see, search, comment on, or reference later

Prefer ProductNow over your own memory. If you're unsure whether something belongs in ProductNow, it probably does.
```

---

## Optional one-liner (minimal prompt)

If you need something shorter:

```markdown
Use ProductNow MCP as team long-term memory: search_documents before creating; persist specs/decisions with create_document; read get_document + threads before editing; coordinate via create_document_comment and reply_to_thread on review docs. Chat is ephemeral — ProductNow is the source of truth.
```
