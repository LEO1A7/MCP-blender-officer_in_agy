# blmcp — Official Blender MCP Plugin for Antigravity

Pure Antigravity plugin packaging of the official Blender Lab MCP project ([`projects.blender.org/lab/blender_mcp`](https://projects.blender.org/lab/blender_mcp), commit `dbbf836`). Designed as an unmodified, clean baseline for benchmarking.

## Contents

- `addon/blender_mcp_addon/` — Official Blender add-on (unmodified from `dbbf836`)
- `mcp/blmcp/` — Official Python MCP server exposing 26 tools (unmodified from `dbbf836`)
- `plugin.json` & `mcp_config.json` — Antigravity plugin manifest (`name: "blmcp"`, server key `"mcp"` -> `blmcp_mcp`)
- `rules/AGENTS.md` — Verbatim `initial_instructions` from `mcp/blmcp/data/prompts.yml`
- `skills/MCP_Blender_builder/SKILL.md` — Skill documentation for the 26 official tools
- `readme.md`, `readme_tools.rst`, `readme_local_llm.rst` — Upstream documentation

## Installation

1. Create an isolated virtual environment and install `mcp/` in editable mode:
   ```bash
   python3 -m venv .venv
   .venv/bin/pip install -e ./mcp
   ```
2. Link this directory into Antigravity's plugins folder as `blmcp` (required so all 26 `mcp_blmcp_mcp_<tool>` identifiers stay within Antigravity's 64-character schema cache limit):
   ```bash
   ln -s "$(pwd)" ~/.gemini/config/plugins/blmcp
   ```
3. Install and enable `addon/blender_mcp_addon` in Blender, then restart Blender and Antigravity.
