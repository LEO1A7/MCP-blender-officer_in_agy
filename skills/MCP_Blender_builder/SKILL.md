---
name: MCP_Blender_builder
description: Official Blender Lab MCP server and add-on (`projects.blender.org/lab/blender_mcp`) providing 26 standard tools for Blender inspection, documentation lookup, viewport navigation, rendering, and Python execution.
---

# Blender MCP (`blender_mcp`)

This skill exposes the official **Blender Lab MCP** server (`mcp/`) and Blender add-on (`addon/blender_mcp_addon/`):

- **Upstream Repository**: `https://projects.blender.org/lab/blender_mcp`
- **Documentation**: `https://www.blender.org/lab/mcp-server/`
- **Bundled Files**:
  - `mcp/`: Python MCP server (`blender-mcp` CLI supporting `--transport stdio|http`, `--host`, `--port`) and bundled RST docs (`mcp/blmcp/data/api/` and `mcp/blmcp/data/manual/`).
  - `addon/blender_mcp_addon/`: Official Blender 5.1+ extension (`id = "mcp"`, installed as `bl_ext.user_default.mcp`) running a local null-byte-delimited TCP socket server (operators `bpy.ops.blmcp.server_start()` / `bpy.ops.blmcp.server_stop()`, interactive timer mode with `check_is_finished` deferred completion, plus `--command blender_mcp` headless blocking mode).

## Connection Configuration

- **Official code defaults** (`blmcp/tools_helpers/connection.py` & `mcp_to_blender_server.py`): `localhost`, port `9876`, Blender executable `blender` (overridable via `BLENDER_MCP_HOST`, `BLENDER_MCP_PORT`, `BLENDER_PATH`).
- **Plugin `mcp_config.json`**: Launches the plugin-local `.venv/bin/blender-mcp` and sets `BLENDER_MCP_PORT=9875` / `BLENDER_PATH=/usr/local/bin/blender` when needed to match the add-on port configured in Blender preferences.

## Instructions

Follow the official server instructions in `mcp/blmcp/data/prompts.yml` (also mirrored in `rules/AGENTS.md` and the MCP server's `instructions.md`):

- Inspect the scene before modifying anything; respect existing hierarchy and naming conventions.
- Prefer dedicated tools over `execute_blender_code`; `execute_blender_code` is a last resort.
- When using `execute_blender_code` or `execute_blender_code_for_cli`, assign a dict to `result` to return structured data; `WeakSandboxForLLM` blocks `sys.exit` and destructive `wm` operators (`quit_blender`, `read_factory_settings`, `read_factory_userpref`, `read_userpref`).
- Deferred completion (`check_is_finished`) is supported only in interactive mode, not background `--command blender_mcp` mode.
- The `*_for_cli` tools inspect or execute against a `.blend` file using `blender --background`; if the target `.blend` is currently open with unsaved edits in the connected Blender session, `synced_blend_for_cli` temporarily writes a clean copy so background execution reflects current edits.

## Official 26 MCP Tools

### Documentation Lookup & Search (3)
- `get_python_api_docs`: Look up bundled Blender Python API RST docs (`*`, `X.*`, exact file, intra-file definition, partial/suggestions; files > 32 KB are summarized to top-level definitions).
- `search_api_docs`: Full-text search over `mcp/blmcp/data/api/` (`max_results`, `context`, `index` pagination).
- `search_manual_docs`: Full-text search over `mcp/blmcp/data/manual/` (`max_results`, `context`, `index` pagination).

### Scene & Blend-File Inspection (14)
- `get_objects_summary`: Return the scene's collection hierarchy, objects, parenting, selection, and visibility.
- `get_object_detail_summary`: Inspect transform, bounding box, constraints, modifiers, materials, data summary, and custom properties for a named object.
- `get_blendfile_summary_datablocks` / `get_blendfile_summary_datablocks_for_cli`
- `get_blendfile_summary_missing_files` / `get_blendfile_summary_missing_files_for_cli`
- `get_blendfile_summary_of_linked_libraries` / `get_blendfile_summary_of_linked_libraries_for_cli`
- `get_blendfile_summary_path_info` / `get_blendfile_summary_path_info_for_cli`
- `get_blendfile_summary_usage_guess` / `get_blendfile_summary_usage_guess_for_cli`

### Screenshots & Rendering (5)
- `get_screenshot_of_area_as_image`: Capture PNG screenshot of a Blender UI area (`VIEW_3D`, `IMAGE_EDITOR`, `OUTLINER`, etc.).
- `get_screenshot_of_window_as_image`: Capture PNG screenshot of the full Blender window.
- `get_screenshot_of_window_as_json`: Return window layout, workspaces, areas, active object, and selection as JSON.
- `render_thumbnail_to_path`: Render a low-resolution preview thumbnail (temporarily overrides render settings).
- `render_viewport_to_path`: Render the scene using current render settings (deferred in interactive mode).

### Workspace & Viewport Navigation (4)
- `jump_to_tab_by_name`: Switch active workspace tab by name.
- `jump_to_tab_by_space_type`: Switch to a workspace matching `space_type` (optionally duplicating if `allow_edits=True`).
- `jump_to_view3d_object_by_name`: Focus the 3D viewport on an object by name (optionally un-hiding/enabling collections if `allow_edits=True`).
- `jump_to_view3d_object_data_by_name`: Focus the 3D viewport on an object matching a data-block name.

### Python Code Execution (2)
- `execute_blender_code`: Execute `bpy` code in the connected Blender session.
- `execute_blender_code_for_cli`: Open a `.blend` file in `blender --background` and execute `bpy` code.
