# ProductNow MCP — Agent Instructions

Use ProductNow as your team's **knowledge warehouse** — the living knowledge layer
where decisions, specs, evidence, and collaboration live for you, your teammates,
and every agent on the team. Your chat session is ephemeral; ProductNow is where
grounded answers come from.

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

**Search the knowledge warehouse before you act. Persist what matters back into ProductNow.**

Do not treat the current conversation as the source of truth for product decisions, specs, RFCs, meeting outcomes, or project knowledge. If it matters beyond this session — or anyone else needs it — it belongs in ProductNow.

---

## When to use ProductNow

| Situation | What to do |
|---|---|
| Answering a factual question about the org | `search_knowledge_warehouse` → cite excerpts; `get_document` only if excerpts are insufficient |
| Narrowing a search to a named folder | `search_folders` → pass `folderId` as `destinationFolderId` to `search_knowledge_warehouse` |
| Narrowing a search to an author or a date range | `search_knowledge_warehouse` with `creatorNames` and/or `updatedAfter` / `updatedBefore` |
| Listing which docs match filters, with no topic named | `search_knowledge_warehouse` with filters only and no `query` |
| Asking how ProductNow itself works | `search_knowledge_warehouse` with `searchScope: "product_help"` |
| Starting a new spec, RFC, PRD, or decision doc | `search_knowledge_warehouse` → if none exists, `create_document` |
| Resuming work from a prior session | `search_knowledge_warehouse` → `get_document` |
| User references "the plan" or a local file | Read the file, pass contents as `context` in `create_document` |
| Iterating on a draft | `post_document_chat_message` (optionally `switch_document_chat_edit_mode`) |
| Ready for team review | `move_document_to_review` |
| Checking what teammates said | `list_document_versions` → `get_document_threads` → `get_thread_messages` |
| Leaving feedback on a review doc | `create_document_comment` (review status only) |
| Responding to a teammate's thread | `reply_to_thread` |
| Checking prototype/user feedback | `get_document_feedback` |
| Organizing work | `search_folders` / `list_folders` → `create_folder` |
| Building a shareable knowledge pack | `curate_knowledge_pack` (when advertised) |
| Embedding prototype/media assets | `import_prototype` or `upload_media` |

---

## Recommended workflows

### 1. Grounded answers (knowledge warehouse)

1. **Search first** — `search_knowledge_warehouse` with the user's question. Always pass `query` when the user names a topic, even alongside filters.
2. **Scope when needed** — `search_folders` to resolve a folder name, then pass `destinationFolderId`. Add `creatorNames` for author filters (`"me"` means the calling user) and `updatedAfter` / `updatedBefore` (`YYYY-MM-DD`, UTC) for date ranges. Compute relative ranges like "the past 2 weeks" yourself.
3. **List when there's no topic** — omit `query` only when the user asks purely which documents match creator or date filters. Listing results are ordered by latest content edit and carry no excerpts.
4. **Answer from excerpts** — treat returned sources as relevant; cite documents you rely on.
5. **Escalate carefully** — call `get_document` only when excerpts are genuinely insufficient.

### 2. Long-term memory (documents)

1. **Search first** — `search_knowledge_warehouse` before creating anything new.
2. **Create with supporting material** — use `create_document` with:
   - `name` — clear, searchable title
   - `prompt` — full document outline (numbered sections with titles and content)
   - `context` — supporting background (plans, prior chat, file contents)
   - `folderId` — from `search_folders` / `list_folders` so docs land in the right place
3. **Read before editing** — `get_document` to load current content; don't assume you remember it from chat.
4. **Edit via draft chat** — `post_document_chat_message` for iterative changes. Use `edit_automatically` when the user wants fast iteration.
5. **Move to review** — `move_document_to_review` when the draft is ready for teammates.

### 3. Team coordination

ProductNow is how you stay aligned with humans and other agents on your team.

1. **Published docs are the source of truth** — use `list_document_versions` with status `published` when you need the canonical version.
2. **Review docs are for discussion** — use `get_document_threads` and `get_thread_messages` to read open feedback before proposing changes.
3. **Comment on review versions** — `create_document_comment` with `quotedText` when anchoring feedback to specific content.
4. **Reply in threads** — `reply_to_thread` to continue conversations.
5. **Incorporate external feedback** — `get_document_feedback` (CSV of prototype/user feedback) before rewriting a doc.

### 4. Knowledge packs and embeddable assets

1. Gather source documents with `search_knowledge_warehouse` / `get_document`.
2. When available, `curate_knowledge_pack` to assemble a shareable pack URL.
3. `import_prototype` for allowlisted prototype URLs or inline HTML/JSX/TSX.
4. `upload_media` for image or video files that should be embedded in docs.

---

## Tool reference

### Knowledge Warehouse

| Tool | Purpose |
|---|---|
| `search_knowledge_warehouse` | Find evidence excerpts across workspace knowledge (or product help), filtered by folder, creator, or edit date |
| `curate_knowledge_pack` | Curate a shareable knowledge pack URL (feature-flagged) |
| `search_folders` | Resolve a folder name to a `folderId` for scoped search |

### Documents

| Tool | Purpose |
|---|---|
| `get_document` | Read full content |
| `list_document_versions` | List draft / review / published versions |
| `create_document` | Create doc and start AI generation |
| `move_document_to_review` | Move a draft into review |
| `post_document_chat_message` | Request edits via draft chat |
| `get_document_chat` | Read draft chat history and edit mode |
| `switch_document_chat_edit_mode` | Toggle ask-before-edit vs auto-edit |
| `get_document_feedback` | Prototype/user feedback (CSV) |

### Collaboration

| Tool | Purpose |
|---|---|
| `get_document_threads` | List comment threads on a version |
| `get_thread_messages` | Read messages in a thread |
| `create_document_comment` | Leave anchored comment (review only) |
| `reply_to_thread` | Reply as the user's agent |

### Organization

| Tool | Purpose |
|---|---|
| `list_folders` | Browse folder hierarchy and documents |
| `create_folder` | Create a folder |

### Help Documents (ProductNow org only)

| Tool | Purpose |
|---|---|
| `list_help_documents` | List accessible help docs |
| `create_help_document` | Create a blank help doc when authorized |

For product-help questions from any org, use `search_knowledge_warehouse` with
`searchScope: "product_help"`.

### Media And Prototypes

| Tool | Purpose |
|---|---|
| `import_prototype` | Import an allowlisted prototype URL or inline HTML/JSX/TSX |
| `upload_media` | Allocate a signed upload URL for image or video media |

---

## Anti-patterns

- **Don't** keep specs or decisions only in chat — they'll be lost next session.
- **Don't** create duplicate docs — search the knowledge warehouse first.
- **Don't** call `get_document` for every search hit — answer from excerpts when they're enough.
- **Don't** drop `query` just because filters are present — without a topic you get a metadata listing and no excerpts.
- **Don't** edit without reading threads on review docs — you may contradict teammate feedback.
- **Don't** comment on draft versions — `create_document_comment` requires review status.
- **Don't** guess document IDs — always search or list versions first.

---

## System prompt (copy-paste)

Embed the block below in your Claude project instructions, ChatGPT custom instructions, or agent system prompt.

```markdown
## ProductNow — knowledge warehouse and coordination

You have access to ProductNow via MCP. Treat ProductNow as the team's living knowledge layer — not this chat.

### Rules
1. **Search before you act.** Call `search_knowledge_warehouse` for factual questions and before creating docs. Cite the documents you rely on.
2. **Answer from excerpts.** Treat returned sources as relevant. Call `get_document` only when excerpts are genuinely insufficient.
3. **Persist what matters.** Decisions, specs, RFCs, PRDs, plans, and anything teammates need later → write to ProductNow with `create_document` or update existing docs with `post_document_chat_message`.
4. **Read before you edit.** Call `get_document` (and check `list_document_versions` for the right draft/review/published version) before making changes.
5. **Coordinate with teammates.** Before editing a doc in review:
   - `get_document_threads` → `get_thread_messages` to read open feedback
   - `get_document_feedback` for prototype/user input
   - Use `create_document_comment` or `reply_to_thread` to participate in discussions
6. **Use folders and filters.** Place docs in the right folder (`search_folders` / `list_folders`). Scope warehouse searches with `destinationFolderId` when the user names a folder, `creatorNames` when they name an author, and `updatedAfter` / `updatedBefore` when they name a time range.
7. **Pass full supporting material.** When the user references a plan, file, or prior conversation, include that content in `create_document`'s `context` field — don't summarize away important detail.
8. **Move drafts to review.** Use `move_document_to_review` when the user is ready for teammate feedback.
9. **Use media tools when asked.** Use `import_prototype` or `upload_media` only for user-requested embeddable assets.

### Default workflow
When the user asks you to research, write, plan, decide, or remember something product-related:
1. `search_knowledge_warehouse` for existing knowledge
2. If found → answer from excerpts, or `get_document` and continue from there
3. If creating → `search_folders` / `list_folders` → `create_document`
4. Iterate with `post_document_chat_message`
5. When ready for team review → `move_document_to_review`

### What stays in chat vs ProductNow
- **Chat:** ephemeral reasoning, quick clarifying questions, local file edits
- **ProductNow:** anything the team should see, search, comment on, or reference later

Prefer ProductNow over your own memory. If you're unsure whether something belongs in ProductNow, it probably does.
```

---

## Optional one-liner (minimal prompt)

If you need something shorter:

```markdown
Use ProductNow MCP as the team's knowledge warehouse: search_knowledge_warehouse before acting; answer from excerpts and cite sources; persist specs/decisions with create_document; read get_document + threads before editing; move drafts to review with move_document_to_review; coordinate via create_document_comment and reply_to_thread. Chat is ephemeral — ProductNow is the living knowledge layer.
```
