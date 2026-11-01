---
name: rodloom
description: Design fishing-rod thread wraps with Rod Loom. Choose real Fuji and ProWrap catalog threads, build solid bands and paired spiral fades, validate dimensions and thread estimates, inspect native PNG previews through the hosted MCP preview_design tool, revise, and generate editable share links. Use for custom rod wraps, trim bands, team or themed color schemes, rod-wrap sizing, Rod Loom design JSON, and visual feedback on band-and-fade patterns. Not for decorative cross-wrap simulation.
metadata:
  author: Rod Loom
  version: "1.0.0"
  homepage: https://rodloom.com/
---

# Rod Loom: design, preview, revise, share

Create a practical fishing-rod thread-wrap plan, inspect its rendered appearance, and deliver an editable **Open in Rod Loom** link. Rod Loom supports solid bands and paired two-thread spiral fades, not decorative cross wraps or taper-compensated cross-wrap spacing.

Resolve relative reference paths against this skill's directory, not the user's working directory. This skill is portable: it does not require a Rod Loom repository checkout.

## Connect and check capabilities

- MCP URL: `https://304heq9dze.execute-api.us-west-2.amazonaws.com/mcp`
- Transport: **Streamable HTTP**; no authentication or persistent session.
- Tool: `preview_design`; a client may prefix its tool name with the server name.
- Input: `{ "design": <full design JSON> }`, not the compact share-link payload.
- Output: validation text, `structuredContent`, and a native `image/png` image content block.

Read [MCP setup](references/mcp-setup.md) when the tool is not available. Installing this skill does **not** connect MCP or enable model vision. Do not change the user's MCP settings or install additional software without permission. When MCP is unavailable, use the HTTP fallback in [the workflow reference](references/workflow.md).

An image-capable model and a client that forwards tool images are needed for direct visual feedback. Never treat a successful request, a base64 string, or validation text as proof that you saw the picture. Do not paste image base64 into the conversation. If you cannot actually inspect the image, explicitly describe the final design as **unpreviewed**.

## 1. Establish the brief

Identify the theme or colors, Size A or D, blank diameter, and target wrap width. Ask for missing measurements when they matter; otherwise propose a first draft with clearly stated assumptions rather than claiming an exact fit. Size D coverage starts at 0.25 mm/turn; Size A starts at 0.15 mm/turn. These are test-wrap starting estimates, not manufacturer guarantees.

For an underwrap, ask for guide-foot length, end margins, and blank diameter if the user needs fit guidance. Translate the requested coverage into a target design width, but explain that Rod Loom does not model the guide foot, taper, or clearance and has no dedicated underwrap-sizing inputs. Do not claim those have been validated.

## 2. Choose real catalog threads

Fetch the public catalog: `https://rodloom.com/assets/catalog.json`.

Match exact catalog `brand`, `sku`, `name`, `finish`, and, when supplied, `line` and `code`. Set `catalog: true`. Do not invent SKUs, change catalog identity fields to match a theme, or substitute an arbitrary brand. Inspect swatches if image tools are available; otherwise choose illustrative `#RRGGBB` preview colors and label them estimates. Stock flags are snapshots, not live inventory. Catalog photos and identities reference Size D; verify corresponding Size A products separately.

Read [workflow and payload reference](references/workflow.md) and [the full paired example](references/example-design.json) before authoring an unfamiliar payload. Use the hosted schema and `tools/list` for current field constraints.

## 3. Build the full design JSON

Required fields: `version: 1`, `name`, `threadSize`, `coverage`, `diameter`, `blank`, `texture`, and `bands`.

- Thread colors and blank: `#RRGGBB`; coverage and diameter: mm.
- Solid band width: `turns × coverage`.
- Paired spiral width: `turns × 2 × coverage`.
- A spiral has `wrap: "spiral"`, `direction: 1` or `-1`, and a complete `secondary` thread object. Primary is outgoing; secondary is incoming. Its turns count finished paired revolutions, not individual strand turns.
- Limits: 250,000 design bytes, at most 500 bands, 1–1,000 integer turns per band, coverage 0.05–1 mm, diameter 1–50 mm, and names up to 80 characters.

Prefer a small, intentional band sequence with readable accents and balanced proportions. Do not assume reversing the array alone correctly mirrors a paired fade: reversing a fade also swaps the outgoing/incoming threads and negates its direction.

## 4. Validate, render, inspect, revise

Call `preview_design` with the full design. A valid result includes `totalWidthMm`, per-thread and total thread estimates, catalog checks, and warnings, alongside rod and flat views in one PNG.

1. If `isError` is true, fix the reported design error. Do not claim an error response is a preview.
2. Read all practical warnings and check the returned width against the brief.
3. If the image is visible to you, inspect contrast, band balance, fade direction, symmetry when intended, and whether accents remain legible. Rod and flat views show different aspects of the same design.
4. Revise if necessary, then render the revised payload again. Judge the final design from its latest image, not a previous draft.
5. If turns are subpixel at preview resolution, disclose that fine detail needs browser zoom. Do not invent details that are not visible.

The server performs structural and catalog validation, not an aesthetic critique. Give your own visual observations only when you actually inspected the image. Dimensions and thread length are planning estimates. Usage excludes setup turns, tag ends, and waste. Colors, metallic and neon effects are illustrative; physical thread samples and a test wrap remain necessary. The PNG is auto-fit, not life-size.

Do not upload secrets or personal data. Rendering does not persist a design, save a library entry, or replace a browser draft. Respect rate limits; avoid unbounded retries on 429 or server errors.

## 5. Deliver an editable design

Follow `https://rodloom.com/agents/share-links.md` and its reference codec to generate a compact share link for the **same full design** you last rendered. A share-link payload differs from import JSON; do not directly base64-encode full import JSON. The entire URL is limited to 8,000 characters. Never truncate it or invent a persistent short link.

Outside a checkout, [the workflow reference](references/workflow.md#generate-a-share-link-without-a-checkout) explains how to obtain the codec, link CLI, and catalog. For oversized links, provide the full UTF-8 JSON file (at most 250,000 bytes) for **Design library → Import** instead.

Return:

- A clickable **Open your [theme] design in Rod Loom** link (or an explicit JSON fallback).
- Approximate width, thread size, coverage, and blank-diameter assumptions.
- A brief visual assessment **only if you saw the final image**; otherwise say **unpreviewed**.
- Important warnings: estimated colors and dimensions, waste allowance, and any Size A or fine-detail caveats relevant to this design.

Opening a link loads a local draft and may ask before replacing existing work; named saves are untouched. Never accept a discard confirmation without the user's permission. Links are encoded, not encrypted; recommend JSON exports for durable backups.
