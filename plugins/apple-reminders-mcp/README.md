# remindctl-mcp

Claude Code plugin for Apple Reminders via [remindctl](https://github.com/steipete/remindctl) CLI.

Say "remind me to deploy on Friday" and your iPhone buzzes.

## Prerequisites

- **macOS 14+**
- **Node.js 18+**
- **remindctl** installed and authorized:

```bash
brew install steipete/tap/remindctl
remindctl authorize
```

## Install

```
/plugin marketplace add namearth5005/namearth5005-plugins
/plugin install remindctl-mcp@namearth5005-plugins
```

That's it. Restart Claude Code and the tools are available.

## Tools

| Tool | Description |
|------|-------------|
| `add_reminder` | Add a reminder with optional due date, list, notes, priority |
| `show_reminders` | Show reminders (today, tomorrow, week, overdue, upcoming, all) |
| `list_reminder_lists` | List all reminder lists or show contents of a specific list |
| `edit_reminder` | Edit title, due date, notes, priority, or move to another list |
| `complete_reminders` | Mark one or more reminders as complete |
| `delete_reminders` | Permanently delete reminders |
| `manage_reminder_list` | Create, rename, or delete reminder lists |

## Usage

```
"remind me to deploy the API on Friday at 3pm"
"what's on my list for today?"
"mark the deploy reminder as done"
"show me all overdue tasks"
```

## License

MIT
