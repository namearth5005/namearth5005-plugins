# apple-reminders-mcp

Claude Code plugin for Apple Reminders via [remindctl](https://github.com/steipete/remindctl) CLI.

Say "remind me to deploy on Friday" and your iPhone buzzes. Or type `/reminders` to browse and manage your reminders interactively.

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
/plugin install apple-reminders-mcp@namearth5005-plugins
```

Restart Claude Code. That's it.

## `/reminders` — Interactive Browser

Type `/reminders` to launch the visual reminder manager:

1. **Browse lists** — See all your reminder lists, pick one
2. **View reminders** — Formatted list with due dates and priorities
3. **Take action** — Add, complete, edit, or delete reminders
4. **Loop** — Stay in the browser until you're done

All selections are presented as clickable options — no typing IDs or commands.

## Direct Tool Usage

You can also use natural language:

```
"remind me to deploy the API on Friday at 3pm"
"what's on my list for today?"
"mark the deploy reminder as done"
"show me all overdue tasks"
"create a new list called Sprint 12"
```

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

## Configuration

On first use of `/reminders`, your preferences are saved to `~/.config/apple-reminders-mcp.json`:

```json
{
  "defaultList": "Work",
  "defaultFilter": "upcoming"
}
```

| Setting | Description | Default |
|---------|-------------|---------|
| `defaultList` | Skip list selection, go straight to this list | none (show all) |
| `defaultFilter` | Default filter when viewing reminders | `"upcoming"` |

Change these anytime via the **Settings** option in `/reminders`.

## License

MIT
