---
name: develop-biorouter-extension
description: Guide for building a BioRouter extension (.brxt file) — covers required structure, manifest schema, Python MCP server setup, and optional bundled skills.
user-invocable: true
---

# Developing a BioRouter Extension (.brxt)

A `.brxt` file is a standard ZIP archive renamed with the `.brxt` extension. BioRouter validates and installs it by unzipping it to `~/.config/biorouter/extensions/<name>/` and running `uv sync` to build the Python virtual environment.

## Required ZIP Contents

```
my-extension.brxt (ZIP archive)
├── manifest.json       ← BioRouter metadata (required)
├── README.md           ← Extension documentation (required)
├── pyproject.toml      ← Python project config (required)
└── src/                ← Python source directory (required)
    └── __init__.py
```

Validation fails if any of the four required entries are missing.

## manifest.json Schema

```json
{
  "name": "myextension",
  "display_name": "My Extension",
  "description": "What this extension does.",
  "version": "1.0.0",
  "entry_point": "myextension",
  "repository": "https://github.com/you/myextension",
  "env_vars": [
    {
      "key": "MY_API_KEY",
      "required": true,
      "auto_propagate": false,
      "description": "API key for the external service.",
      "secret": true,
      "default": ""
    }
  ]
}
```

**Required fields:** `name`, `display_name`, `description`, `version`, `entry_point`, `repository`, `env_vars`

**`env_vars` field reference:**

| Field | Type | Purpose |
|---|---|---|
| `key` | string | Environment variable name |
| `required` | bool | Whether BioRouter blocks startup if missing |
| `auto_propagate` | bool | Whether to pass the var through automatically |
| `description` | string | Shown to the user in the UI |
| `secret` | bool | Masks the value in the UI |
| `default` | string | Optional fallback value |

## Python Package (pyproject.toml + src/)

The extension is a Python package that implements an MCP server. Use `uv` for dependency management — BioRouter runs `uv sync` on install.

Minimal `pyproject.toml`:

```toml
[project]
name = "myextension"
version = "1.0.0"
requires-python = ">=3.11"
dependencies = [
  "mcp>=1.0.0",
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

The `entry_point` field in `manifest.json` must match the Python module name (the package importable as `python -m <entry_point>`).

## Optional: Bundled Skills

Skills can be shipped inside the `.brxt` and are installed atomically alongside the extension:

```
skills/
  my-skill-slug/
    SKILL.md
  another-skill/
    SKILL.md
    supporting-file.md
```

Each `SKILL.md` must begin with valid YAML frontmatter:

```markdown
---
name: My Skill Name
description: One-line description of what this skill does.
---

Skill body in markdown...
```

Skills install to `~/.config/biorouter/extensions/<name>/skills/<slug>/SKILL.md` and are auto-discovered by the agent on startup.

## Building the .brxt File

```bash
# Create the ZIP and rename to .brxt
cd my-extension-dir
zip -r ../myextension.brxt manifest.json README.md pyproject.toml src/ skills/
```

Or using Python:

```python
import zipfile, pathlib

with zipfile.ZipFile("myextension.brxt", "w", zipfile.ZIP_DEFLATED) as zf:
    for path in pathlib.Path(".").rglob("*"):
        if path.is_file() and ".venv" not in path.parts:
            zf.write(path)
```

Exclude `.venv/`, `__pycache__/`, and any build artifacts.

## Installation & Uninstallation

BioRouter installs by:
1. Unzipping all contents to `~/.config/biorouter/extensions/<name>/`
2. Running `uv sync` (timeout: 120s) to build the virtual environment

Uninstallation removes the entire `~/.config/biorouter/extensions/<name>/` directory, including any bundled skills.

## Validation Checklist

Before distributing a `.brxt`, verify:

```
[ ] ZIP contains manifest.json at root
[ ] ZIP contains README.md at root
[ ] ZIP contains pyproject.toml at root
[ ] ZIP contains src/ directory
[ ] manifest.json has all required fields
[ ] env_vars is a JSON array (even if empty: [])
[ ] Each SKILL.md has --- frontmatter with name and description
[ ] .venv/ and __pycache__/ are excluded from the ZIP
[ ] uv sync completes cleanly from the unzipped directory
```
