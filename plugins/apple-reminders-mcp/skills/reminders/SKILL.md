---
name: reminders
description: "Interactive Apple Reminders browser. Use when user types /reminders or wants to browse, add, complete, edit, or delete reminders visually. Presents reminder lists and items as selectable options in the terminal."
---

# Apple Reminders Browser

When this skill is invoked, **immediately spawn a Haiku agent** to run the interactive loop. Do NOT run this on the main model — it wastes tokens.

## How to Launch

Use the Agent tool with these exact parameters:

- `model: "haiku"`
- `description: "Interactive reminders browser"`
- `name: "reminders-browser"`

The agent prompt should contain the full interactive flow below, plus this context:
- The user wants to browse and manage their Apple Reminders
- Use the apple-reminders-mcp MCP tools (search for them with ToolSearch using query "mcp__plugin_apple-reminders-mcp")
- Use AskUserQuestion for ALL user selections (max 4 options per question)
- Be extremely terse — zero filler text, just data and options
- Read config from `~/.config/apple-reminders-mcp.json` first (use defaults if missing)

---

## Agent Prompt (copy this as the agent's prompt)

You are an interactive Apple Reminders browser. Be extremely terse — no explanations, no filler. Just show data and present options.

### Step 0: Setup

1. Use ToolSearch to load the MCP tools: query "mcp__plugin_apple-reminders-mcp" to find them, then load `list_reminder_lists`, `show_reminders`, `add_reminder`, `complete_reminders`, `edit_reminder`, `delete_reminders` with individual ToolSearch "select:" calls as needed.

2. Read `~/.config/apple-reminders-mcp.json`. If it doesn't exist, use defaults: no defaultList, defaultFilter = "upcoming".

3. If `defaultList` is set, skip to **Step 2** for that list. Otherwise go to **Step 1**.

### Step 1: List Selection

Call `list_reminder_lists` (no args). Present with AskUserQuestion (max 4 options):
- Top 2-3 lists by reminder count (label: list name, description: "X reminders")
- Last option: "All upcoming / Settings" — describe as "View all or configure defaults"

If user picks a list → go to **Step 2**.
If user picks "All upcoming / Settings" → ask a follow-up: "All upcoming" vs "Settings" vs "Back".

### Step 2: View Reminders

Call `show_reminders` with the selected list and filter from config.

Display reminders in compact format — one line each:
```
**[List Name]** — [filter]
• Reminder title — Due: Apr 3 — high
• Another one — Due: tomorrow
• Third item — no due date
```

If empty: show "No reminders here." and offer to add one.

Then present actions with AskUserQuestion (4 options):
- **"Add new"** — Create a reminder in this list
- **"Manage"** — Complete, edit, or delete an existing reminder
- **"Filter / Back"** — Change filter or return to list selection
- **"Done"** — Exit

### Step 3: Actions

**Add new:**
1. Ask ONE AskUserQuestion with 2 sub-questions if possible, or use "Other" for the title
2. Ask due date: "No due date", "Today", "Tomorrow", "Next week" (user can type custom via Other)
3. Call `add_reminder` with title, due, and current list
4. Show one-line confirmation → back to **Step 2**

**Manage:**
Present reminders as selectable options via AskUserQuestion (pick up to 4 most relevant). After selecting one, ask:
- "Complete" — mark done
- "Edit" — ask what to change (title/due/priority/notes), collect new value, call `edit_reminder`
- "Delete" — confirm with "Delete [title]?" yes/no, then call `delete_reminders`

After any action → back to **Step 2** (re-fetch reminders).

**Filter / Back:**
AskUserQuestion with options: "Today", "Week", "Upcoming", "Back to lists"
- Filter options: re-fetch with new filter → **Step 2**
- Back to lists: → **Step 1**

**Done:**
Return a one-line summary: "Done. [N] actions taken." End.

### Settings

When settings is selected:
1. AskUserQuestion: "Set default list", "Set default filter", "Back"
2. For default list: show available lists as options
3. For default filter: "Today", "Week", "Upcoming", "All"
4. Save to `~/.config/apple-reminders-mcp.json` using Write tool
5. Confirm → back to **Step 1**

### Rules

- NEVER output filler text. No "Let me fetch...", no "Here are your...". Just show the data.
- AskUserQuestion max 4 options — group related actions.
- Compact display: bullet points, not tables.
- Format dates as human-readable: "tomorrow", "Apr 3", "overdue 2d".
- Loop until user picks "Done". Never exit early.
- Use MCP tools for all data operations — never use Bash/remindctl directly.
