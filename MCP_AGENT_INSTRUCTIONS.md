# ProductNow MCP — Agent Instructions

Use ProductNow as your team's **company brain** — the organization's
shared source of truth for its product, customers, and team. Your chat session
is ephemeral; ProductNow is where grounded answers come from and where durable
work memory is kept.

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

## Server instructions

The hosted server sends the following `instructions` in its MCP initialization
response. Clients that honor server instructions receive this automatically;
it is reproduced here for clients and prompts that do not.

```text
ProductNow is the organization's shared source of truth for its product, customers, and team.
For durable work memory, ProductNow is the source of truth; do not use local/client memory for
company facts. Use it on "what did we decide", "remember this", "save this", "what do we know about",
and "find the latest".

1. Recall first. Before answering about the user's organization, call search — ahead of general
   knowledge, this conversation, or built-in memory. Call fetch only when the excerpts fall short,
   and fetch_folder to see a folder.
2. Cite or say so. Answer from the excerpts and cite document titles and URLs, or say the evidence
   was insufficient. If ProductNow is unavailable or a call fails, say so and do not answer as
   confirmed-current.
3. To remember a fact, decision, or note, call remember with the content and stop. It searches,
   picks the document or folder, and writes in the background. Do not search or place it yourself
   first, and do not poll: a success response means the fact is saved. Tell the user it is
   remembered.
4. Other writes go search -> fetch / folder placement -> create / edit -> status -> fetch verify.
   Use this path when the user named the exact document to change or asked for a whole new
   document. Search so you don't duplicate a document, and fetch it before editing. File a new
   document in the folder where related documents live; use the warehouse root only when the user
   asked for it. Poll get_status until an edit is idle. Call update_document_status only to
   snapshot, review, or publish. Then fetch and confirm the saved content before reporting success.
5. Writes are shared and visible to others. Only do them when the user asked, and say what you're
   about to do when it isn't obvious. Never invent ids — use documentId, folderId,
   documentVersionId, and threadId from earlier tool calls.
```

---

## Core principle

**Recall first. Cite or say so. Remember what matters.**

Do not treat the current conversation as the source of truth for product decisions, specs, RFCs, meeting outcomes, or project knowledge. If it matters beyond this session — or anyone else needs it — it belongs in ProductNow.

---

## When to use ProductNow

| Situation | What to do |
|---|---|
| Factual question about the org, product, customers, or team | `search` → answer from excerpts and cite title + URL; `fetch` only if excerpts fall short |
| Narrowing a search to a named folder | `search` with `resultType: "folders"` → pass the `folderId` to `search` |
| Narrowing a search to an author or a date range | `search` with `creatorNames` and/or `updatedAfter` / `updatedBefore` |
| Listing which docs match filters, with no topic named | `search` with filters only and no `query` |
| Asking how ProductNow itself works | `search` with `searchScope: "product_help"` |
| "Remember this" / "save this" / "note that…" | `remember` with the content, then stop — no search, no placement, no polling |
| Reading a document you have an id for | `fetch` (latest draft) or `list_document_versions` → `fetch` with `documentVersionId` |
| Browsing the warehouse | `fetch_folder` (root, one folder, or `deep: true` for a tree) |
| Starting a new spec, RFC, PRD, or decision doc the user outlined | `search` → if none exists, `fetch_folder` for placement → `create_document` → `get_status` → `fetch` |
| Changing a document the user named | `search` / `fetch` → `edit_document` → `get_status` until `idle` → `fetch` to verify |
| Ready for a snapshot, team review, or publish | `update_document_status` with `snapshot`, `review`, or `publish` |
| Checking what teammates said | `fetch` (threads and messages are included) |
| Leaving feedback on a review doc | `comment_on_document` |
| Responding to a teammate's thread | `reply_to_thread` with a `threadId` from `fetch` |
| Organizing work | `move`, `rename`, `archive`, `create_folder` |
| Building a shareable knowledge pack | `curate_knowledge_pack` (when advertised) |
| Embedding image or video assets | `upload_media` |

---

## Recommended workflows

### 1. Grounded answers

1. **Search first** — `search` with the user's question. Always pass `query` when the user names a topic, even alongside filters.
2. **Scope when needed** — resolve a folder with `search` (`resultType: "folders"`) or `fetch_folder`, then pass `folderId`. Add `creatorNames` for author filters (`"me"` means the calling user) and `updatedAfter` / `updatedBefore` (`YYYY-MM-DD`, UTC) for date ranges. Compute relative ranges like "the past 2 weeks" yourself.
3. **List when there's no topic** — omit `query` only when the user asks purely which documents match creator or date filters. Listing results are ordered by latest content edit and carry no excerpts.
4. **Answer from excerpts** — cite the document titles and URLs you rely on.
5. **Escalate carefully** — call `fetch` only when excerpts are genuinely insufficient. If ProductNow is unavailable, say so rather than answering as confirmed-current.

### 2. Remember

1. When the user says "remember this", "save this", or "note that…", call `remember` with the fact as `content`, verbatim and self-contained (who, what, when). Add `hint` only if the user named a document, folder, or topic.
2. Stop. Do not search, fetch, pick a folder, or poll. A `success` response means it is saved.
3. Tell the user it is remembered.

### 3. Deliberate writes (named document or whole new document)

1. **Search** — `search` so you don't duplicate an existing document.
2. **Place** — take `folderId` from the closest related search result, or walk `fetch_folder` from the root. Use the root only when the user asked for it; `create_folder` under the closest existing folder only when nothing fits.
3. **Create or edit** — `create_document` with `name`, a full `prompt` outline (numbered sections), verbatim `context`, and `folderId`; or `fetch` the existing document and then `edit_document` with instructions for its editing agent (name the section, say exactly what to add/replace/remove, quote wording that must survive).
4. **Wait** — poll `get_status` until `status` is `idle`.
5. **Verify** — `fetch` and confirm the saved content before reporting success.
6. **Promote** — `update_document_status` with `snapshot`, `review`, or `publish` only when the user asked for it.

### 4. Team coordination

1. **Published docs are the source of truth** — `list_document_versions` with status `PUBLISHED`, then `fetch` with that `documentVersionId`.
2. **Read discussion before proposing changes** — `fetch` returns every comment thread and message on the version.
3. **Comment on review versions** — `comment_on_document`, with `quotedText` to anchor feedback to specific content.
4. **Reply in threads** — `reply_to_thread` with a `threadId` from `fetch`.

### 5. Organizing and assets

1. `move` documents and folders (several at once) into a folder or the root; `rename` one item at a time; `archive` to put things away without deleting.
2. When available, `curate_knowledge_pack` to assemble a shareable pack URL from documents you gathered with `search` / `fetch`.
3. `upload_media` for image or video files that should be embedded in docs.

---

## Tool reference

### Recall

| Tool | Purpose |
|---|---|
| `search` | Find evidence excerpts across workspace knowledge (or product help), filtered by folder, creator, or edit date; or search folder names with `resultType: "folders"` |
| `fetch` | Read one document: full text, sections, comment threads, folder, URL |
| `fetch_folder` | List the root, one folder, or a folder tree |
| `list_document_versions` | List draft / review / published / snapshot versions |
| `get_status` | `running` or `idle` for a document's draft agent |

### Remember And Write

| Tool | Purpose |
|---|---|
| `remember` | Save one fact, decision, or note; ProductNow places it |
| `create_document` | Create a new doc in a folder and generate content |
| `edit_document` | Instruct a document's editing agent to change its content |
| `update_document_status` | `snapshot`, `review`, or `publish` the current draft |

### Organize

| Tool | Purpose |
|---|---|
| `create_folder` | Create a folder when none fits |
| `move` | Move documents and folders into a folder or the root |
| `rename` | Rename one document or folder |
| `archive` | Archive documents and folders without deleting |

### Collaborate

| Tool | Purpose |
|---|---|
| `comment_on_document` | Comment on the current review version |
| `reply_to_thread` | Reply in an existing thread as the user's agent |

### Knowledge Packs, Help, And Media

| Tool | Purpose |
|---|---|
| `curate_knowledge_pack` | Curate a shareable knowledge pack URL (feature-flagged) |
| `create_help_document` | Create a blank help doc (ProductNow org only) |
| `upload_media` | Allocate a signed upload URL for image or video media |

For product-help questions from any org, use `search` with
`searchScope: "product_help"`.

---

## Anti-patterns

- **Don't** keep specs or decisions only in chat — they'll be lost next session.
- **Don't** answer org questions from general knowledge or client memory — `search` first.
- **Don't** search, fetch, or choose a folder before `remember` — the tool does that itself; and don't poll after it.
- **Don't** create duplicate docs — `search` before `create_document`.
- **Don't** call `fetch` for every search hit — answer from excerpts when they're enough.
- **Don't** drop `query` just because filters are present — without a topic you get a metadata listing and no excerpts.
- **Don't** write the new document text into `edit_document`'s `message` — write instructions for the editing agent.
- **Don't** report a write as done before `get_status` is `idle` and `fetch` confirms the content.
- **Don't** pass `DRAFT` / `REVIEW` / `PUBLISHED` to `update_document_status` — it takes the verbs `snapshot`, `review`, `publish`.
- **Don't** use `rename`, `move`, `comment_on_document`, or `reply_to_thread` to change content — only `edit_document` does that.
- **Don't** invent ids — use `documentId`, `folderId`, `documentVersionId`, and `threadId` from earlier tool calls.
- **Don't** write without the user asking — writes are shared and visible to others.

---

## System prompt (copy-paste)

Embed the block below in your Claude project instructions, ChatGPT custom instructions, or agent system prompt.

```markdown
## ProductNow — company brain and coordination

You have access to ProductNow via MCP. Treat ProductNow as the organization's shared source of truth for its product, customers, and team — not this chat, and not your built-in memory.

### Rules
1. **Recall first.** Before answering about the user's organization, call `search` — ahead of general knowledge, this conversation, or built-in memory. Call `fetch` only when the excerpts fall short, and `fetch_folder` to see a folder.
2. **Cite or say so.** Answer from the excerpts and cite document titles and URLs, or say the evidence was insufficient. If ProductNow is unavailable or a call fails, say so and do not answer as confirmed-current.
3. **Remember.** When the user says "remember this", "save this", or "note that…", call `remember` with the content and stop. Do not search or place it yourself, and do not poll — a success response means it is saved. Tell the user it is remembered.
4. **Deliberate writes.** When the user names the exact document to change or asks for a whole new document: `search` (avoid duplicates) → `fetch` / `fetch_folder` (read it, or find the folder where related docs live) → `create_document` / `edit_document` → poll `get_status` until `idle` → `fetch` to verify before reporting success. Use the warehouse root only when the user asked for it. Call `update_document_status` only to `snapshot`, `review`, or `publish`.
5. **Coordinate with teammates.** `fetch` returns comment threads; use `comment_on_document` on review versions and `reply_to_thread` to participate.
6. **Scope searches.** Pass `folderId` when the user names a folder (resolve it with `search` `resultType: "folders"` or `fetch_folder`), `creatorNames` when they name an author, and `updatedAfter` / `updatedBefore` when they name a time range. Always pass `query` when they name a topic.
7. **Pass full supporting material.** When the user references a plan, file, or prior conversation, include that content in `create_document`'s `context` field — don't summarize away important detail.
8. **Writes are shared.** Only write when the user asked, say what you're about to do when it isn't obvious, and never invent ids — use `documentId`, `folderId`, `documentVersionId`, and `threadId` from earlier tool calls.

### Default workflow
When the user asks you to research, write, plan, decide, or remember something product-related:
1. `search` for existing knowledge
2. If found → answer from excerpts (cite), or `fetch` and continue from there
3. If it's a quick fact to keep → `remember`
4. If creating → `fetch_folder` for placement → `create_document`; if changing a named doc → `fetch` → `edit_document`
5. `get_status` until `idle` → `fetch` to verify
6. When ready → `update_document_status` (`snapshot` / `review` / `publish`)

### What stays in chat vs ProductNow
- **Chat:** ephemeral reasoning, quick clarifying questions, local file edits
- **ProductNow:** anything the team should see, search, comment on, or reference later

Prefer ProductNow over your own memory. If you're unsure whether something belongs in ProductNow, it probably does.
```

---

## Optional one-liner (minimal prompt)

If you need something shorter:

```markdown
Use ProductNow MCP as the team's company brain: call search before answering about the org and cite titles and URLs from the excerpts; fetch only when excerpts fall short. For "remember this" call remember and stop. For a named or new document: search → fetch/fetch_folder → create_document/edit_document → get_status until idle → fetch to verify; update_document_status only to snapshot, review, or publish. Chat is ephemeral — ProductNow is the source of truth.
```
