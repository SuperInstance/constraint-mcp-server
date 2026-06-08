# constraint-mcp-server

**MCP (Model Context Protocol) server for the constraint theory ecosystem** — query constraints, snap pitches, diagnose sequences, generate music, and render audio from any MCP-compatible AI tool.

## What Is This?

This is an [MCP server](https://modelcontextprotocol.io/) that exposes the SuperInstance constraint theory tools as MCP tools. It's designed to work with **Copilot for Eclipse**, Claude Desktop, and any other MCP-compatible client.

The server provides tools for:

- **Pitch snapping** — snap notes to the nearest Eisenstein lattice point
- **Constraint funneling** — apply gravitational pull toward a target pitch
- **Diagnostics** — run 4-order Goodman diagnostic on note sequences
- **Music generation** — generate music in a given mode + terrain
- **Audio rendering** — render notes to WAV audio bytes
- **Terrain listing** — list available musical terrains

## Quick Start

### Installation

```bash
pip install constraint-mcp-server
```

Or from source:

```bash
git clone https://github.com/SuperInstance/constraint-mcp-server.git
cd constraint-mcp-server
pip install -e .
```

### Dependencies

- Python ≥ 3.10
- `mcp[cli]` ≥ 1.0.0
- The constraint ecosystem (lazy-loaded):
  - [constraint-substrate](https://github.com/SuperInstance/constraint-substrate)
  - [constraint-synth](https://github.com/SuperInstance/constraint-synth)
  - [constraint_instrument](https://github.com/SuperInstance/constraint_instrument)

### Running the Server

```bash
# As a module
python -m constraint_mcp_server

# As an installed script
constraint-mcp
```

### Configuring with MCP Client

Add to your MCP client configuration (e.g., `claude_desktop_config.json`):

```json
{
  "servers": {
    "constraint-ecosystem": {
      "command": "python3",
      "args": ["-m", "constraint_mcp_server"],
      "env": {
        "PYTHONPATH": "/path/to/constraint-mcp-server:/path/to/constraint-substrate/python:/path/to/constraint_instrument:/path/to/constraint-synth",
        "CONSTRAINT_WORKSPACE": "/path/to/workspace"
      },
      "type": "stdio"
    }
  }
}
```

## MCP Tools Provided

### `constraint_snap`

Snap a pitch to the nearest Eisenstein lattice point.

```json
{
  "pitch": 60,
  "scale": "major",
  "octave_range": [3, 6]
}
```

Returns the snapped pitch and the snap distance (how far it moved).

### `constraint_funnel`

Apply gravitational pull toward a target pitch, simulating constraint funnel dynamics.

```json
{
  "current_pitch": 62,
  "target_pitch": 60,
  "strength": 0.7,
  "scale": "major"
}
```

Returns the funnel-adjusted pitch.

### `constraint_diagnose`

Run a 4-order Goodman diagnostic on a sequence of notes. Analyzes:

1. **First order**: Note-to-note intervals
2. **Second order**: Interval-to-interval changes (acceleration)
3. **Third order**: Rate of change of acceleration
4. **Fourth order**: Structural coherence measure

```json
{
  "notes": [60, 62, 64, 65, 67, 69, 71, 72],
  "key": "C"
}
```

Returns a diagnostic report with scores at each order.

### `constraint_generate`

Generate music in a given mode and terrain.

```json
{
  "mode": "dorian",
  "terrain": "rolling_hills",
  "bars": 8,
  "tempo": 120
}
```

Returns generated MIDI-like note data.

### `constraint_render`

Render notes to WAV audio bytes.

```json
{
  "notes": [
    {"pitch": 60, "duration": 0.5, "velocity": 80},
    {"pitch": 64, "duration": 0.5, "velocity": 80},
    {"pitch": 67, "duration": 1.0, "velocity": 90}
  ],
  "sample_rate": 44100
}
```

Returns base64-encoded WAV audio.

### `constraint_terrain_list`

List all available musical terrains for generation.

```json
{}
```

Returns a list of terrain names with descriptions.

## Architecture

```
constraint_mcp_server/
├── __init__.py          # Server implementation + tool handlers
├── __main__.py          # Entry point for `python -m`
mcp-config.json          # Example MCP client configuration
pyproject.toml           # Build config with entry point
```

### Design Principles

1. **Lazy loading**: The constraint ecosystem libraries are loaded on first use, not at import time
2. **Workspace-relative paths**: Automatically finds sibling repos in the same workspace
3. **stdio transport**: Uses MCP's stdio transport for maximum compatibility
4. **Graceful degradation**: Tools that need unavailable libraries return helpful error messages

## Integration Examples

### With Claude Desktop

1. Install: `pip install constraint-mcp-server`
2. Add to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "constraints": {
      "command": "python3",
      "args": ["-m", "constraint_mcp_server"]
    }
  }
}
```

3. Restart Claude Desktop
4. Ask Claude: "Snap pitch 62 to the nearest lattice point in C major"

### With Copilot for Eclipse

The server is designed for integration with Copilot for Eclipse via the MCP protocol:

1. Configure the server in Eclipse's MCP settings
2. Use the constraint tools in your coding workflow
3. Generate constraint-aware music directly in your IDE

### With Custom MCP Client

```python
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

server_params = StdioServerParameters(
    command="python3",
    args=["-m", "constraint_mcp_server"],
)

async with stdio_client(server_params) as (read, write):
    async with ClientSession(read, write) as session:
        await session.initialize()
        
        # List available tools
        tools = await session.list_tools()
        
        # Snap a pitch
        result = await session.call_tool("constraint_snap", {
            "pitch": 62,
            "scale": "major"
        })
```

## Constraint Theory Background

The tools in this server are based on constraint theory as developed in the SuperInstance research program:

- **Constraints** define valid regions in a musical space
- **Snapping** moves a note to the nearest valid point on a constraint lattice
- **Funnels** simulate gravitational dynamics that pull notes toward attractors
- **Terrains** define the overall constraint landscape (hills, valleys, ridges)
- **Diagnostics** measure how well a sequence satisfies constraints at multiple orders

The underlying mathematics uses **Eisenstein integers** (the ring ℤ[ω] where ω = e^(2πi/3)) to model musical constraint lattices. This provides:

- Natural 3-dimensional structure (perfect fifth, major third, octave)
- Efficient computation via integer lattice operations
- Connection to the Tonnetz and neo-Riemannian theory

## Related Projects

| Repository | Description |
|-----------|-------------|
| [constraint-substrate](https://github.com/SuperInstance/constraint-substrate) | Multi-language constraint substrate (C, Python, Rust) |
| [constraint-synth](https://github.com/SuperInstance/constraint-synth) | Constraint-based audio synthesis |
| [constraint-viz](https://github.com/SuperInstance/constraint-viz) | Multi-scale constraint visualization oscilloscope |
| [constraint-theory-core](https://github.com/SuperInstance/constraint-theory-core) | Core constraint theory mathematics |
| [constraint-toolkit](https://github.com/SuperInstance/constraint-toolkit) | Python constraint toolkit |
| [fortran-constraint-checking](https://github.com/SuperInstance/fortran-constraint-checking) | High-performance Fortran constraint checker |
| [constraint-instrument](https://github.com/SuperInstance/constraint-instrument) | Live constraint-based musical instrument |

## Development

```bash
git clone https://github.com/SuperInstance/constraint-mcp-server.git
cd constraint-mcp-server
pip install -e .
```

### Adding a New Tool

1. Define the tool schema in `list_tools()`
2. Handle the tool in `call_tool()`
3. Add any needed lazy imports
4. Update this README

### Testing

```bash
# Test the server starts
python -m constraint_mcp_server &
sleep 2
kill %1
```

## Citation

```bibtex
@software{constraint_mcp_server_2026,
  title = {constraint-mcp-server: MCP Server for the Constraint Theory Ecosystem},
  author = {SuperInstance Research},
  year = {2026},
  url = {https://github.com/SuperInstance/constraint-mcp-server}
}
```

## License

MIT — see [LICENSE](LICENSE) for details.

---

Part of the [SuperInstance](https://github.com/SuperInstance) constraint theory ecosystem.
