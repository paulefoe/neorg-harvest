# neorg-harvest

A [Neorg](https://github.com/nvim-neorg/neorg) module for extracting and aggregating tasks from your journal entries.

## Features

- Extract tasks from journal entries by anchor name
- Aggregate tasks across multiple journal files
- Generate summary files with dated task lists
- Works with Neorg's nested journal structure (`journal/YYYY/MM/DD.norg`)

## Installation

### vim-plug

```vim
Plug 'nvim-neorg/neorg'
Plug 'paulefoe/neorg-harvest'
```

### lazy.nvim

```lua
{
    "nvim-neorg/neorg",
    dependencies = {
        "paulefoe/neorg-harvest",
    },
}
```

## Setup

Add the module to your Neorg configuration:

```lua
require("neorg").setup({
    load = {
        ["core.defaults"] = {},
        ["core.dirman"] = {
            config = {
                workspaces = {
                    notes = "~/notes",
                },
            },
        },
        ["core.journal"] = {
            config = {
                strategy = "nested",  -- Uses YYYY/MM/DD.norg structure
            },
        },
        -- Add the harvest module
        ["external.harvest"] = {
            config = {
                -- Optional: specify workspace (defaults to current)
                workspace = nil,
                -- Optional: journal folder name (defaults to "journal")
                journal_folder = "journal",
            },
        },
    },
})
```

## Usage

### Command

```vim
:Neorg harvest <anchor> <date>
```

### Examples

**Harvest tasks for a month (current year):**
```vim
:Neorg harvest Work December
```

**Harvest tasks for a specific date range:**
```vim
:Neorg harvest ProjectA 2025-12-01 2025-12-31
```

### How it works

Given journal entries like this:

**journal/2025/12/01.norg:**
```norg
** Today's Tasks
   - (x) Work
   -- (x) Review pull request
   -- (x) Update documentation
   - ( ) Exercise
   - (x) Read a book
```

**journal/2025/12/02.norg:**
```norg
** Today's Tasks
   - (x) Work
   -- (x) Fix bug in API
   -- (x) Deploy to production
```

Running `:Neorg harvest Work December` will:

1. Scan all `.norg` files in `journal/2025/12/`
2. Find list items that match "Work" exactly
3. Extract **only** the nested children (the `--` items, not siblings)
4. Create a summary file at `journal/2025/12/harvest_Work.norg`

**Output:**
```
01.12.2025 Review pull request
01.12.2025 Update documentation
02.12.2025 Fix bug in API
02.12.2025 Deploy to production
```

Note that "Exercise" and "Read a book" are **not** included because they are siblings of Work, not children.

## Configuration

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `workspace` | `string\|nil` | `nil` | Workspace to use. If nil, uses current workspace. |
| `journal_folder` | `string` | `"journal"` | Name of the journal folder in your workspace. |

## Requirements

- [Neorg](https://github.com/nvim-neorg/neorg) >= 8.0.0
- Neovim >= 0.10.0

## License

MIT
