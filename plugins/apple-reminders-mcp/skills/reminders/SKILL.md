---
name: reminders
description: "Interactive Apple Reminders browser. Use when user types /reminders or wants to browse, add, complete, edit, or delete reminders visually. Presents reminder lists and items as selectable options in the terminal."
---

# Apple Reminders Browser

An interactive terminal-based reminder manager. Guides the user through browsing lists, viewing reminders, and taking actions — all via selectable options.

## Setup: Load Config

Before starting the interactive flow, read the user's config:

1. Read `~/.config/apple-reminders-mcp.json` using the Read tool
2. If the file doesn't exist, use these defaults:
   - `defaultList`: none (show all lists)
   - `defaultFilter`: "upcoming"
3. If `defaultList` is set, skip directly to **Show Reminders** for that list

## Step 1: Show All Lists

Fetch all reminder lists using `list_reminder_lists` (no arguments).

Present them to the user using AskUserQuestion with these options:
- Each list as a selectable option (use list name as label, show reminder count in description if available)
- Add an **"All upcoming"** option that shows reminders across all lists
- Add a **"Settings"** option to configure defaults

Example:
```
"Which reminder list?"
Options:
- "Work" — Your work tasks
- "Personal" — Personal reminders
- "Resolution" — Resolution list
- "All upcoming" — Show upcoming reminders from all lists
- "Settings" — Configure default list and filter
```

If the user selects **Settings**, go to the **Settings Flow** below.

## Step 2: Show Reminders

Fetch reminders using `show_reminders`:
- If a specific list was selected: pass `list` parameter
- If "All upcoming" was selected: pass only the `filter` parameter
- Use the `defaultFilter` from config (or "upcoming" if not set)

Format the results as a clean, readable display:

```
## [List Name] — [filter] reminders

 1. Buy groceries          Due: Apr 3    Priority: high
 2. Call dentist            Due: Apr 5    Priority: none
 3. Review PR #42           Due: tomorrow Priority: medium

3 reminders shown.
```

Then present actions using AskUserQuestion:
```
"What would you like to do?"
Options:
- "Add a new reminder"
- "Complete a reminder"
- "Edit a reminder"
- "Delete a reminder"
- "Change filter" — Switch between today/tomorrow/week/overdue/upcoming/completed/all
- "Back to lists"
- "Done" — Exit the reminder browser
```

## Step 3: Execute Action

### Add a Reminder
1. Use AskUserQuestion to ask for the reminder title (open-ended "Other" input)
2. Then ask for optional fields with AskUserQuestion:
   - **Due date**: "No due date", "Today", "Tomorrow", "This week", or let them type a custom date
   - **Priority**: "None", "Low", "Medium", "High"
3. Call `add_reminder` with the collected fields, using the current list
4. Show confirmation, then return to **Step 2**

### Complete a Reminder
1. Present all incomplete reminders as selectable options via AskUserQuestion (use multiSelect: true so they can complete multiple at once)
2. Call `complete_reminders` with the selected IDs
3. Show confirmation, then return to **Step 2**

### Edit a Reminder
1. Present reminders as selectable options via AskUserQuestion
2. After selecting one, ask what to change:
   - "Title", "Due date", "Priority", "Notes", "Move to another list"
3. Collect the new value
4. Call `edit_reminder` with the ID and changed field
5. Show confirmation, then return to **Step 2**

### Delete a Reminder
1. Present reminders as selectable options via AskUserQuestion
2. After selecting, show a confirmation: "Delete [title]? This cannot be undone."
   - Options: "Yes, delete it", "Cancel"
3. If confirmed, call `delete_reminders` with the ID
4. Show confirmation, then return to **Step 2**

### Change Filter
Present filter options via AskUserQuestion:
- "Today", "Tomorrow", "This week", "Overdue", "Upcoming", "Completed", "All"

Apply the selected filter and return to **Step 2** with updated results.

### Back to Lists
Return to **Step 1**.

### Done
Thank the user and end the skill. Do not loop further.

## Settings Flow

When the user selects "Settings" from the list view:

1. Use AskUserQuestion to ask which setting to change:
   - "Set default list" — pick from available lists or "None"
   - "Set default filter" — pick from filter options
   - "Back to lists"

2. For **Set default list**: fetch lists with `list_reminder_lists`, present as options
3. For **Set default filter**: present filter options (today/tomorrow/week/upcoming/all)

4. Save the updated config to `~/.config/apple-reminders-mcp.json` using the Write tool:
```json
{
  "defaultList": "Work",
  "defaultFilter": "upcoming"
}
```

5. Confirm the change, then return to **Step 1**.

## Important Rules

- **Always use AskUserQuestion** for selections — never ask the user to type IDs or indices manually
- **Loop back** to the list view after every action — don't exit unless the user chooses "Done"
- **Show reminder counts** when displaying lists so the user knows which lists have items
- **Format dates** in human-readable form (e.g. "Apr 3", "tomorrow", "overdue by 2 days")
- **Use the MCP tools** from the apple-reminders-mcp server for all data operations — never call remindctl directly via Bash
- **Handle empty states** gracefully: "No reminders in this list. Would you like to add one?"
