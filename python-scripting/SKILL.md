---
name: python-scripting
description: Python scripting conventions. Apply whenever writing, editing, or reviewing any Python code.
user-invocable: false
---

# Python Scripting Conventions

Always follow these rules when writing any Python code:

## Package management

Always use `uv` for package management unless it is genuinely infeasible (e.g., system-level installs, CI environments that don't support it).

- Create virtual environments with: `uv venv .venv`
- Install packages with: `uv pip install <pkg>`
- Place the `.venv` inside the project or study-specific folder, never in a shared location
- Never use `pip install` directly when `uv` is available

## Comments

- Keep comments minimal — one short inline comment at most per logical block
- Only add a comment if the logic is genuinely non-obvious; omit otherwise
- Do not write block comments or multi-line comment headers
