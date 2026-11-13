# videobackgroundremover.ai MCP server

This repository publishes the official [`server.json`](./server.json) metadata for the [videobackgroundremover.ai](https://videobackgroundremover.ai/) remote MCP server. It is intended for the [official MCP Registry](https://registry.modelcontextprotocol.io/) and for clients that discover remote servers from that metadata.

The product is a paid, remote Model Context Protocol (MCP) service for video background removal. Clients connect over HTTPS; work runs on the videobackgroundremover.ai service rather than as a local binary. Still-image background-removal tools and self-hosted pipelines exist elsewhere; this server is for video, with credit-based billing and OAuth sign-in on the same site that processes the files.

## Remote MCP endpoint

- MCP (Streamable HTTP): https://videobackgroundremover.ai/mcp
- Authentication: OAuth 2.1 (PKCE, dynamic client registration). Do not put an API key in the MCP remote headers.
- Developer page: https://videobackgroundremover.ai/video-background-remover-api
- OpenAPI 3.1 (script HTTP API, Bearer key): https://videobackgroundremover.ai/openapi.json

Registry name: `ai.videobackgroundremover/video-background-remover`

## Tools

These tools are exposed by the remote MCP server:

| Tool | Purpose |
| --- | --- |
| `get_account` | Read the signed-in account and credit balance. |
| `buy_credits` | Open the pricing page to buy credits. |
| `estimate_cost` | Estimate credit usage before processing. |
| `create_upload` | Reserve credits and receive a single-use PUT URL (15 minutes). Upload the file, then call `remove_background` with the `upload_id`. |
| `remove_background` | Start a job from exactly one public HTTPS `video_url` or one `upload_id` from `create_upload`. |
| `start_job` | Same as `remove_background` with an `upload_id`; kept for compatibility. |
| `get_job` | Read one job. Poll until the status is `done`, `failed`, `canceled`, or `no_subject`. |
| `list_jobs` | List the 20 most recent jobs. |
| `export_format` | Export a completed job in another format. |
| `cancel_job` | Cancel a job and refund unused credits. |

## Credits

Processing is billed in credits. When `create_upload` or `remove_background` is called without a `seconds` argument, the service reserves 600 credits (the default 300-second cap) up front and settles against the actual duration after processing. Passing `seconds` (and optionally `max_credits`) limits that reservation. Use `estimate_cost` first if you want a quote before reserving.

## Client configuration

Add the remote server, then complete the OAuth sign-in flow in the client. No API key header is required for MCP.

### Claude Code

```bash
claude mcp add --transport http videobackgroundremover-ai https://videobackgroundremover.ai/mcp
```

Or in `.mcp.json`:

```json
{
  "mcpServers": {
    "videobackgroundremover-ai": {
      "type": "http",
      "url": "https://videobackgroundremover.ai/mcp"
    }
  }
}
```

### Cursor

In `.cursor/mcp.json` or `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "videobackgroundremover-ai": {
      "url": "https://videobackgroundremover.ai/mcp"
    }
  }
}
```

Other MCP clients that support Streamable HTTP remotes can use the same URL. Configuration field names vary; prefer an HTTP / Streamable HTTP transport and the URL above.

## License

This metadata repository is released under the [MIT License](./LICENSE).

— The videobackgroundremover.ai team
