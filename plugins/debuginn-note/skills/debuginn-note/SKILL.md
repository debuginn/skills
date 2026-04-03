---
name: debuginn-note
description: Organize and summarize the current conversation into a structured Markdown document, preview it inline, then save to a user-specified directory with global memory. Use when the user wants to export, archive, or document the current conversation as a clean Markdown note.
---

# Noteflow — Conversation to Markdown

Use this skill when the user wants to turn the current conversation into a clean, structured Markdown document and save it to disk.

## Full Workflow

### Step 1 — Generate the Markdown document

Analyze the entire conversation and produce a well-structured Markdown document:

- Extract the core topic as a concise `# Title`
- Add a `> Summary` blockquote (1–3 sentences)
- Organize key content under `##` sections (decisions, findings, code, action items, etc.)
- Omit filler turns, repeated clarifications, and tool noise
- Use fenced code blocks for any code snippets
- Add a `---` footer with metadata:
  ```markdown
  ---
  > 📅 Generated: YYYY-MM-DD  
  > 🗂 Source: [conversation topic]
  ```

### Step 2 — Preview inline

Output the full generated document inside a fenced markdown block so the user can review it before saving:

````
```markdown
[generated document here]
```
````

Ask the user:
- "内容是否需要调整？如需修改请告诉我，确认后我们继续保存。"
- Wait for confirmation or edit requests before proceeding to Step 3.

### Step 3 — Determine save directory

Read the global config file `~/.config/noteflow/config.json`:

```bash
cat ~/.config/noteflow/config.json
```

- **Config exists and has `save_dir`**: show it to the user:
  - "上次保存目录是 `{save_dir}`，是否继续使用？还是指定新目录？"
- **Config missing or `save_dir` is empty**: ask the user:
  - "请指定笔记保存目录（例如 ~/Documents/notes）："

This config file is tool-agnostic — any editor, script, or AI tool can read and write it.

### Step 4 — Confirm filename

Generate a default filename from the document title based on the **language the user is using**:

- **Chinese**: use the Chinese title directly, replace spaces with `-`, strip punctuation  
  Example: `技术方案讨论` → `技术方案讨论.md`
- **English**: lowercase, replace spaces with `-`, strip special characters  
  Example: `API Design Review` → `api-design-review.md`
- **Mixed / other**: use the dominant language rule

Format: `{slug}.md`

Show the full target path and ask for confirmation:
- "将保存至 `{dir}/{filename}`，确认吗？"

### Step 5 — Check for existing file

Before saving, check if the file already exists at the target path:

```bash
ls "{target_path}"
```

If the file **exists**:
- Show a warning: "⚠️ 文件已存在：`{target_path}`"
- Offer three options:
  1. **覆盖** — overwrite the existing file
  2. **重命名** — append a suffix (e.g., `-v2`, `-1`) to avoid conflict
  3. **取消** — abort the save

If the file **does not exist**, proceed directly to Step 6.

### Step 6 — Save the file

Write the document using the Write tool to the confirmed path.

Confirm success:
- "✅ 已保存至 `{target_path}`"

### Step 7 — Persist directory to global config

After a successful save, write the directory to the shared config file:

1. **Create directory if needed**:
   ```bash
   mkdir -p ~/.config/noteflow
   ```

2. **Write config** (preserve any existing keys, only update `save_dir`):
   ```json
   {
     "save_dir": "{dir}"
   }
   ```
   Write to `~/.config/noteflow/config.json`.

3. **Also save a Claude memory reference** (so Claude can locate the config quickly):
   - Write to `~/.claude/projects/-/memory/debuginn-note.md`:
     ```markdown
     ---
     name: debuginn-note
     description: Noteflow global config path and save directory reference.
     type: reference
     ---

     Config file: `~/.config/noteflow/config.json`  
     Current save_dir: `{dir}`

     **How to apply:** Always read the config file first; treat this memory as a fast-path cache only.
     ```
   - Add a pointer line to `~/.claude/projects/-/memory/MEMORY.md`.

The config file is the single source of truth. The Claude memory entry is a pointer, not the authority.

## Modification Workflow

When the user wants to edit the document after preview (Step 2) or after saving:

1. Accept natural-language edit instructions ("把第二节改成…", "加一个 action items 部分", "标题改为…")
2. Apply edits and show the updated document again in a fenced block
3. Ask for confirmation before re-saving
4. If the file was already saved, follow Step 5 (overwrite/rename/cancel)

## Document Structure Template

```markdown
# {Title}

> {One-line summary of the conversation}

## {Section 1}

{Content}

## {Section 2}

{Content}

---
> 📅 Generated: {YYYY-MM-DD}  
> 🗂 Source: {topic or context}
```

## Notes

- Always preview before saving — never write to disk without user confirmation.
- `~/.config/noteflow/config.json` is the single source of truth for save directory — tool-agnostic, readable by any editor, script, or AI tool.
- Claude memory at `~/.claude/projects/-/memory/debuginn-note.md` is a fast-path pointer only; always re-read the config file to get the current value.
- Use absolute paths when saving files; expand `~` to the actual home directory.
- If `~/.config/noteflow/` does not exist, create it before writing the config.
