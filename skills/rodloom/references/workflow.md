# Payload, catalog, preview, and sharing reference

## Public resources

- Agent quickstart: https://rodloom.com/agents/quickstart.md
- Authoring guide: https://rodloom.com/agents/design-guide.md
- Full JSON schema: https://rodloom.com/agents/design.schema.json
- Catalog: https://rodloom.com/assets/catalog.json
- Share-link contract: https://rodloom.com/agents/share-links.md
- Codec: https://rodloom.com/design-links.js

Use the bundled [example-design.json](example-design.json) as a complete paired-spiral example. Its width is 8 mm at 0.25 mm coverage on a 15 mm blank. It uses catalog identities from a snapshot; if the hosted catalog changes, resolve the current metadata and validate again. The preview colors are illustrative.

## Catalog lookup

A catalog item supplies identity fields, including brand, SKU, name, finish, line, and code. Thread objects in full design JSON require `name`, `color`, `finish`, `brand`, `sku`, and `catalog: true`. Add `turns` to each band. Copy optional `line` and `code` exactly when supplied. Do not use an arbitrary swatch URL as a thread identity.

For example, using shell tools and Python 3 in a working directory chosen for the design:

```sh
curl --fail --location https://rodloom.com/assets/catalog.json -o catalog.json
python -c "import json; c=json.load(open('catalog.json',encoding='utf-8')); print(json.dumps([t for t in c['items'] if t['brand']=='ProWrap' and t['line']=='ColorFast'][:5],indent=2))"
```

The catalog has swatch paths, not manufacturer-certified RGB values. Supply approximate preview colors and disclose that they are estimates.

## Full design format

The bundled example contains all required top-level fields and two threads connected by a paired fade. A minimal solid-band design is:

```json
{
  "version": 1,
  "name": "Yellowfin trim",
  "threadSize": "D",
  "coverage": 0.25,
  "diameter": 15,
  "blank": "#263a40",
  "texture": true,
  "bands": [{
    "name": "101 Yellowfin",
    "color": "#d8b43c",
    "finish": "regular",
    "brand": "ProWrap",
    "line": "ColorFast",
    "sku": "CFS-D-101",
    "code": "101",
    "catalog": true,
    "wrap": "solid",
    "turns": 40
  }]
}
```

This covers approximately 10 mm. Replace identities only with matching current catalog entries. For paired spirals, set `wrap: "spiral"`, `direction: 1` or `-1`, and `secondary` to the full incoming thread object. Spiral turns are finished paired revolutions; each covers two thread widths. The primary thread is outgoing. Optional `quickColors` holds at most 500 full thread objects.

Total wrap width is the sum of `turns × coverage` for solids and `turns × 2 × coverage` for spirals. The renderer and validation service calculate helical thread length using the blank diameter plus coverage; both strands contribute in a paired fade. Do not substitute total width for thread consumption.

## MCP preview

Discover `preview_design` through the configured client and call:

```text
preview_design({"design": <full design object>})
```

Check `isError`, read validation text or `structuredContent`, then inspect the native image block if it is visible. Validation does not judge visual quality. Catalog mismatches and invalid geometry return tool errors with no image. The JSON-RPC envelope has a 300,000-byte limit; the nested design has a 250,000-byte limit and at most 500 bands.

## HTTP fallback

When MCP is unavailable, use the same hosted backend without a checkout:

```sh
API_BASE=https://304heq9dze.execute-api.us-west-2.amazonaws.com
curl --fail-with-body "$API_BASE/api/validate" \
  -H 'Content-Type: application/json' --data-binary @design.json
curl --fail "$API_BASE/api/preview" \
  -H 'Content-Type: application/json' --data-binary @design.json -o preview.png
```

Open `preview.png` with an image-viewing tool before describing its visual quality. An HTTP download alone does not expose the image to a model. If the agent lacks network, file, or image tools, provide the full JSON with explicit assumptions and state that the result is unpreviewed. Do not claim you rendered or validated it unless you actually did so.

HTTP failures return JSON errors: 400 malformed input, 413 excessive size, 415 incorrect content type, 422 invalid design/catalog metadata, and 405 incorrect method. Responses are `no-store`. For 429 or temporary 5xx failures, use only bounded retries with backoff or report the failure. Do not interpret an error body as a PNG.

## Generate a share link without a checkout

Use the published reference codec instead of inventing a URL format. With network access and Node.js 18+, download these trusted Rod Loom files into a separate working directory, preserving relative paths:

```sh
mkdir -p agents assets
curl --fail --location https://rodloom.com/design-links.js -o design-links.js
curl --fail --location https://rodloom.com/agents/create-link.cjs -o agents/create-link.cjs
curl --fail --location https://rodloom.com/assets/catalog.json -o assets/catalog.json
# Inspect the downloaded code before executing it.
node agents/create-link.cjs design.json
```

Do not overwrite the user's unrelated files to create this layout. The CLI uses the codec's catalog checks and compact payload format, then prints a `https://rodloom.com/#design=...` URL. Provide it as a clickable link. Do not execute code from an untrusted replacement URL. If file/network execution is unavailable, follow the published compact schema and codec using the agent's available tools; otherwise return a JSON file instead of a fabricated link.

The full design and compact URL contracts differ. The URL must not exceed 8,000 characters. If it is too long, provide the full design JSON for **Design library → Import**. Never truncate a link. Do not describe encoded links as encrypted, persistent short links, or cloud saves. Opening a link may ask before replacing a local draft; that choice belongs to the user. Export JSON for a durable backup.

## Final response checklist

- Is the delivered link or JSON for the exact design last rendered?
- Did you actually see the final PNG? If not, say **unpreviewed**.
- Does the response state approximate width, Size A/D, coverage, and assumed blank diameter?
- Are warnings about illustrative colors, dimensions, waste, and Size A product equivalents included where relevant?
- Have you avoided claiming physical guide-foot fit, saved library entries, successful browser navigation, or cloud persistence without actually performing/satisfying those actions?
