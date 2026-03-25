---
name: things
description: |
  Things CLI to manage tasks from the command line.

  Use for:
  - Listing tasks and projects as JSON
  - Creating and editing tasks/projects/headings
  - Completing, trashing, purging, and moving tasks
  - Managing areas, tags, and checklist items

  The CLI returns JSON by default.
---

Manage Things data with `things-cli`.

## Tips

- Use the full binary path: `~/go/bin/things-cli`
- Output is JSON arrays/objects by default (no `--json` flag needed)
- IDs are UUID-like strings in the `uuid` field
- Common item fields: `uuid`, `title`, `note`, `status`, `isProject`, `inTrash`, `schedule`
- Optional relation/date fields: `scheduledDate`, `deadlineDate`, `parentIds`, `areaIds`
- Dates use `YYYY-MM-DD`
- Read commands may hit cloud rate limits (`429 Too Many Requests`) if called too frequently

## Usage

Use:

```
~/go/bin/things-cli <command> [args]
```

If the things-cli binary is not installed, install it with:

```
go install github.com/arthursoares/things-cloud-sdk/cmd/things-cli@main
```

## Commands Reference

```
things-cli list [--today] [--inbox] [--area NAME] [--project NAME]
things-cli projects
things-cli show <id>

things-cli create <title> [--note ...] [--when today|anytime|someday|inbox] \
  [--deadline YYYY-MM-DD] [--scheduled YYYY-MM-DD] [--project <id>] \
  [--heading <id>] [--area <id>] [--tags <id>,...] \
  [--type task|project|heading] [--uuid <id>] [--checklist <item-1,item-2,...>]

things-cli edit <id> [--title <title>] [--note ...] [--when today|anytime|someday|inbox] \
  [--deadline YYYY-MM-DD] [--scheduled YYYY-MM-DD] [--area <id>] \
  [--project <id>] [--heading <id>] [--tags <id>,...]

things-cli complete <id>
things-cli move-to-today <id>
things-cli add-checklist <id> <item-1,item-2,item-3>
things-cli trash <id>
things-cli purge <id>

things-cli create-area <title> [--tags <id>,...] [--uuid <id>]
things-cli create-tag <title> [--shorthand <key>] [--parent <id>]

echo '[{"cmd":"complete","uuid":"<id>"},{"cmd":"trash","uuid":"<id>"}]' | things-cli batch
```

## Output Notes

- `status` and `schedule` are numeric enums.
- `isProject: true` identifies project records.
- `parentIds` links tasks to project/heading containers.
- `areaIds` links projects/tasks to an area.

## Example Usage

```bash
# List work to process
things-cli list --today
things-cli list --inbox
things-cli projects

# Create a task and assign metadata
things-cli create <title> --when today --deadline YYYY-MM-DD --project <id>

# Inspect and update an item
things-cli show <id>
things-cli edit <id> --title <title>

# Complete or remove
things-cli complete <id>
things-cli trash <id>
things-cli purge <id>

# Batch updates via stdin JSON
echo '[{"cmd":"complete","uuid":"<id>"},{"cmd":"trash","uuid":"<id>"}]' | ~/go/bin/things-cli batch
```
