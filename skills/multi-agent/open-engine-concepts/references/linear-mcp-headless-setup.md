# Linear MCP Setup on a Headless Hermes Server

## Context

Setting up Linear MCP on a remote server (no display) where Hermes runs as a Telegram gateway. The user's local machine (Mac) has Hermes CLI but the MCP needs to live on the server.

## Method 1: API Key (Recommended for headless)

Much simpler than OAuth. The user generates a **Personal API Key** from Linear (`https://linear.app/settings/security`) and you configure it as a Bearer token header.

### 1. Add via Python yaml (the only clean way)

`hermes config set` puts entries at the wrong YAML level (top level, not under `mcp_servers:`).
`hermes mcp add` is interactive and doesn't accept `--env` for URL-based servers.

The working approach is to inject it directly via Python's yaml library:

```python
import yaml
with open('/root/.hermes/config.yaml') as f:
    data = yaml.safe_load(f)
data['mcp_servers']['linear'] = {
    'url': 'https://mcp.linear.app/mcp',
    'enabled': True,
    'timeout': 300,
    'connect_timeout': 60,
    'headers': {
        'Authorization': 'Bearer <API_KEY>'
    }
}
with open('/root/.hermes/config.yaml', 'w') as f:
    yaml.dump(data, f, default_flow_style=False)
```

### 2. Test

```bash
hermes mcp test linear
```

Expected output:
```
Testing 'linear'...
Transport: HTTP → https://mcp.linear.app/mcp
  Authorization: ***
✓ Connected
✓ Tools discovered: 47
```

### 3. Verify the config looks right

```bash
hermes mcp list | grep linear
# → linear  https://mcp.linear.app/mcp  all  ✓ enabled
```

## Method 2: Headless OAuth piping (fallback)

Use only when the user can't generate an API key.

```bash
printf 'Y\n\nY\n' | hermes mcp add linear --url https://mcp.linear.app/mcp
```

This pipes answers: Y (auth required), empty (no token), Y (save anyway).

Result: server saved but `✗ disabled`. User must run `hermes mcp login linear` from their local machine (opens browser for OAuth).

## Method 3: Interactive `hermes mcp add` prompts

If running interactively, the prompts are:
1. `Does this server require authentication? [Y/n]:` → Y
2. `API key / Bearer token:` → paste the API key
3. `Save config anyway (you can test later)? [y/N]:` → y (after 401)

The API key is saved to `~/.hermes/.env` as `MCP_LINEAR_API_KEY`.

## Quirks

- **`hermes config set` is broken for MCP**: `hermes config set mcp_servers.linear '{...}'` places the entry at the config TOP LEVEL, not under `mcp_servers:`. This means Hermes won't load it as an MCP server. Always use `hermes mcp add` OR Python yaml injection.
- **The env var `MCP_LINEAR_API_KEY` is set only when passing the key interactively** during `hermes mcp add`. If added via yaml headers, the env var won't be set and doesn't need to be.
- **Restart required for tool discovery**: After adding, Linear MCP tools (47 tools: `list_teams`, `list_issues`, `save_issue`, etc.) are only available to the agent after Hermes gateway restart.
- **API key is workspace-scoped**: The key works for all teams the user has access to in that workspace.
- **Try `curl` first to verify the key**: `curl -s -X POST -H "Authorization: Bearer <key>" -H "Content-Type: application/json" -d '{"query":"{teams{nodes{id name}}}"}' https://api.linear.app/graphql` — if this returns data, the key works and the MCP will work too.
