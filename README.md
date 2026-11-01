# Rod Loom agent skill

Design fishing-rod thread wraps with real Fuji and ProWrap catalog threads, inspect rod-and-flat PNG previews through MCP, revise the design, and deliver editable share links.

## Install

```sh
npx skills add jaywolters/rodloom-skills --skill rodloom
```

Select your agent and installation scope. The default is project-local; use `--global` only if you want a user-wide installation.

Inspect [SKILL.md](skills/rodloom/SKILL.md), the [MCP setup](skills/rodloom/references/mcp-setup.md), and the [workflow reference](skills/rodloom/references/workflow.md) before using the skill.

## Connect MCP separately

Installing a skill adds instructions. It does not automatically connect MCP, grant permissions, or enable vision.

- Server: `https://304heq9dze.execute-api.us-west-2.amazonaws.com/mcp`
- Transport: Streamable HTTP
- Tool: `preview_design`
- Authentication: none; do not submit sensitive data.

Use an image-capable model and a client that forwards tool images. The skill also documents an HTTP download-and-open fallback. An unseen design must be described as **unpreviewed**.

## Example request

> Use the Rod Loom skill to design a gold-and-green fishing rod wrap, Size D, approximately 30 mm wide on a 15 mm blank. Choose real catalog threads, render and inspect the rod and flat views, revise if needed, and give me an Open in Rod Loom link. Tell me whether you actually saw the preview.

## Resources

- [Skills.sh listing](https://skills.sh/jaywolters/rodloom-skills/rodloom)
- [Rod Loom designer](https://rodloom.com/)
- [Installation guide](https://rodloom.com/agents/install-skill.md)
- [Hosted MCP guide](https://rodloom.com/agents/mcp.md)
- [Agent resource index](https://rodloom.com/llms.txt)

This repository contains only the publicly distributed skill and references. It does not contain the application source or its history. Colors, dimensions, and thread consumption are estimates. Rendering does not persist designs, and Rod Loom does not simulate decorative cross wraps.

Skills.sh lists public skills through normal Skills CLI installation telemetry; indexing and ranking are managed by Skills.sh, not this repository.
