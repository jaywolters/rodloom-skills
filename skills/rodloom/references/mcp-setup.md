# Connect Rod Loom MCP

Installing the Rod Loom skill adds instructions, not a tool connection. Ask before modifying a user's MCP configuration. Use an image-capable model and confirm that the client forwards MCP image content to the model.

## Shared connection details

- URL: `https://304heq9dze.execute-api.us-west-2.amazonaws.com/mcp`
- Transport: remote **Streamable HTTP**, not legacy SSE.
- Authentication: none. Do not add or request an API key.
- Tool: `preview_design`, taking `{ "design": <full design JSON> }`.
- Full server guide: https://rodloom.com/agents/mcp.md

The URL is on the dedicated AWS API origin, not `rodloom.com/mcp`. The tool is read-only, but the design is sent to a public hosted renderer; do not submit sensitive information. No image or design is persisted.

## Claude Code

With the user's permission, add the remote server using Claude Code's CLI:

```sh
claude mcp add --transport http rodloom https://304heq9dze.execute-api.us-west-2.amazonaws.com/mcp
```

This uses the client's default scope; do not silently add `--scope user` or alter unrelated configuration. Use `/mcp` inside Claude Code to check the connection and tool permissions. Follow the current [Claude Code MCP instructions](https://code.claude.com/docs/en/mcp) if the installed CLI differs.

## Cursor

With the user's permission, merge this entry into the project's `.cursor/mcp.json`, preserving other servers:

```json
{
  "mcpServers": {
    "rodloom": {
      "url": "https://304heq9dze.execute-api.us-west-2.amazonaws.com/mcp"
    }
  }
}
```

Enable the server under Cursor's MCP settings, then verify that `preview_design` appears. Follow [Cursor's MCP instructions](https://cursor.com/docs/context/mcp) for current configuration locations and image support. A connected server alone does not establish that the active model can see its returned images.

## Other clients

Add a remote HTTP MCP server named `rodloom` using the URL above. Configuration keys and approval scopes vary; consult that client's documentation rather than assuming its JSON is interchangeable with Cursor or Claude Code. This skill does not include a stdio bridge. For clients limited to stdio or lacking MCP, use [the HTTP fallback](workflow.md#http-fallback) rather than pretending the tool is connected.

## Verify before designing

1. Initialize the connection and list tools through the client. `preview_design` publishes its full input schema.
2. Call the tool with [example-design.json](example-design.json) as the `design` argument.
3. Confirm `isError` is false, validation reports `totalWidthMm: 8`, and the result contains a native PNG image content block with rod and flat views.
4. Confirm you can actually inspect the image. If not, use an image-viewing tool on a downloaded PNG, or call the design unpreviewed.

The server uses stateless JSON responses and negotiates MCP `2025-06-18` or `2025-03-26`. It does not create sessions or offer a persistent GET/SSE stream. GET and DELETE return 405 by design. A 403 can indicate a rejected browser Origin: allowed browser origins are the Rod Loom HTTPS sites and localhost/loopback clients. Non-browser clients generally omit Origin. Do not bypass Origin checks by impersonating another website.
