---
name: reminders
description: "Interactive Apple Reminders browser. Use when user types /reminders or wants to browse, add, complete, edit, or delete reminders visually. Presents reminder lists and items as selectable options in the terminal."
---

# Apple Reminders Browser

**Speed:** Before starting, suggest the user switch to fast mode (`/fast`) if not already — this is a simple CRUD flow that doesn't need full reasoning.

**Style:** Zero filler. No "Let me fetch...", no "Here are your reminders." Just data and options. Be as terse as possible throughout.

## Setup

1. Read `~/.config/apple-reminders-mcp.json` using Read tool
2. If missing: defaults = no defaultList, defaultFilter = "upcoming"
3. If `defaultList` is set → skip to **View Reminders** for that list

## Step 1: List Selection

Call `list_reminder_lists` (no args). Present with AskUserQuestion (max 4 options):
- Top 2-3 lists sorted by reminder count (label: name, description: "X reminders")
- Last option: "All upcoming / Settings"

If user picks "All upcoming / Settings" → follow-up AskUserQuestion: "All upcoming" vs "Settings" vs "Back".

## Step 2: View Reminders

Call `show_reminders` with selected list + filter from config.

Display compact — sorted by due date, one bullet per reminder:
```
**Resolution** — upcoming
• Kiran — Apr 20
• Jaz — Apr 24
• Marine — May 23
16 reminders.
```

Present actions (max 4 options):
- **"Add new"** — Create a reminder in this list
- **"Manage"** — Complete, edit, or delete existing
- **"Filter / Back"** — Change filter or switch lists
- **"Done"** — Exit

## Step 3: Actions

### Add new
1. AskUserQuestion — user types title via "Other"
2. AskUserQuestion — due date: "No due date", "Today", "Tomorrow", "Next week"
3. Call `add_reminder` with title, due, current list
4. One-line confirmation → back to Step 2

### Manage
Present up to 4 reminders (soonest due first) via AskUserQuestion. After selecting:
- AskUserQuestion: "Complete", "Edit", "Delete", "Back"
- **Complete**: call `complete_reminders` → back to Step 2
- **Edit**: ask what to change (title/due/priority/notes) → collect value → call `edit_reminder` → back to Step 2
- **Delete**: confirm "Delete [title]?" → call `delete_reminders` → back to Step 2

### Filter / Back
AskUserQuestion: "Today", "Week", "Upcoming", "Back to lists"
- Filter → re-fetch → Step 2
- Back → Step 1

### Done
"Done. [N] actions taken." Stop.

## Settings

1. AskUserQuestion: "Set default list", "Set default filter", "Back"
2. Default list: present available lists as options
3. Default filter: "Today", "Week", "Upcoming", "All"
4. Save to `~/.config/apple-reminders-mcp.json` via Write tool
5. Confirm → back to Step 1

## Rules

- Zero filler text — just data and options
- AskUserQuestion max 4 options — group related actions
- Compact bullets, not tables
- Dates as human-readable: "tomorrow", "Apr 3", "overdue 2d"
- Loop until "Done"
- MCP tools only — never Bash/remindctl directly
- Empty state: "No reminders. Add one?" with Add option
