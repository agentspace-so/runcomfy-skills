---
name: nano-banana-2
displayName: "🍌 Nano Banana 2 — Pro Pack on RunComfy"
description: >
  Generate images with Google Nano Banana 2 (Gemini-family flash-tier
  text-to-image) on RunComfy — bundled with the model's documented
  prompting patterns so the agent gets sharper output than naive
  prompting against the same model. Knows Nano Banana 2's strengths
  (rapid iteration, in-image typography rendering, predictable framing,
  optional web-grounded context), the resolution-tier pricing, the
  safety-tolerance dial, and when to route to Nano Banana Pro / GPT
  Image 2 / Flux 2 / Seedream instead. Calls
  `runcomfy run google/nano-banana-2/text-to-image` through the local
  RunComfy CLI. Triggers on "nano banana", "nano-banana-2", "nano banana 2",
  "google image gen", "gemini image", or any explicit ask to generate
  with this model.
emoji: "🍌"
homepage: https://www.runcomfy.com
license: MIT
---

# 🍌 Nano Banana 2 — Pro Pack on RunComfy

[runcomfy.com](https://www.runcomfy.com/?utm_source=skills.sh&utm_medium=skill&utm_campaign=nano-banana-2) · [Model page](https://www.runcomfy.com/models/google/nano-banana-2?utm_source=skills.sh&utm_medium=skill&utm_campaign=nano-banana-2) · [GitHub](https://github.com/agentspace-so/runcomfy-skills/tree/main/nano-banana-2)

Google **Nano Banana 2** — the flash-tier text-to-image model in the Gemini family — hosted on the **RunComfy Model API**. Optimized for ideation, social-thumbnail batches, and rapid drafts with strong in-image typography.

**This skill bundles the model's documented prompting patterns** — subject-first declarative grammar, exact-text-quoting for typography, web-grounding when needed, sibling-routing — so the agent gets the model's strongest output on the first or second try, instead of burning credits on prompts the model wasn't going to nail.

## Install

```bash
npx skills add agentspace-so/runcomfy-skills --skill nano-banana-2 -g
```

## Why this skill (vs calling the model raw)

A bare `runcomfy run google/nano-banana-2/text-to-image --input '{"prompt":"..."}'` runs, but you'll spend several iterations relearning what Nano Banana 2 actually responds to. This skill packs that knowledge in:

- **Subject-first structure** — `Subject → action → environment → style → camera/lighting`. Front-load the subject, trail with directives.
- **Exact text quoting** — for in-image typography, **quote the exact characters** and specify placement (`"the headline 'OPEN STUDIO' centered, large, bold sans-serif"`).
- **Resolution tier choice** — `0.5K` (cheap drafts) → `1K` (default) → `2K` / `4K` (final). Higher resolution costs proportionally more — the agent picks tier by intent, not always max.
- **Aspect ratio is concrete** — 11 supported values (`auto`, `21:9`, `16:9`, `3:2`, `4:3`, `5:4`, `1:1`, `4:5`, `3:4`, `2:3`, `9:16`). The agent maps user intent to one of these — never asks for arbitrary ratios.
- **`num_images` for variant tests** — 1–4 per request. Use 4 for ideation rounds; 1 for final.
- **`enable_web_search`** — turn on when the prompt references current events / real entities the model wouldn't have memorized. Costs extra; off by default.
- **`safety_tolerance`** — 1 (strict) to 6 (permissive). Default 4. Route to a higher value only when content is genuinely benign and getting incorrectly blocked.
- **Sibling routing** — when Nano Banana 2 wins (rapid drafts, social thumbnails, batch variants, in-image text) vs when Nano Banana Pro / GPT Image 2 / Flux 2 / Seedream beat it (image edit precision, multilingual signage, stylization, max resolution).

The agent calling this skill gets the model's strongest output on the first or second iteration — same model, sharper result.

## When to pick this model (vs siblings)

Nano Banana 2 is the **flash-tier** of the Google image-gen line. Pick it when iteration speed and predictable framing matter more than maximum detail.

| You want | Use |
|---|---|
| Rapid drafts, social thumbnails, batch variants | **Nano Banana 2** ✓ |
| In-image typography with predictable rendering | **Nano Banana 2** ✓ |
| Web-grounded image (current events / real entities) | **Nano Banana 2** + `enable_web_search` |
| Image **edit** (preserve subject, swap background) | **Nano Banana Edit** (sibling skill) |
| Heavy stylization, painterly look | Flux 2 |
| Maximum prompt adherence + multilingual text | GPT Image 2 |
| 2K–4K hero shots, max realism | Seedream 5 |
| Hyperrealistic portrait | Nano Banana Pro |

If the user said "Nano Banana" / "nano-banana-2" / "Gemini image" explicitly, route here regardless. If they said "Nano Banana" without specifying 2 vs Pro, default to **Pro** for portraits and **2** for everything else.

## Prerequisites

1. **RunComfy CLI** — `npm i -g @runcomfy/cli` or `curl -fsSL https://runcomfy.com/install.sh | sh`
2. **RunComfy account** — `runcomfy login` opens a browser device-code flow.
3. **CI / containers** — set `RUNCOMFY_TOKEN=<token>` instead of `runcomfy login`.

## Endpoints + input schema

### `google/nano-banana-2/text-to-image`

| Field | Type | Required | Default | Notes |
|---|---|---|---|---|
| `prompt` | string | yes | — | Subject-first description. |
| `num_images` | int | no | 1 | 1–4. Use 4 for ideation rounds. |
| `seed` | int | no | 0 | Reuse for reproducibility. |
| `aspect_ratio` | enum | no | `auto` | `auto`, `21:9`, `16:9`, `3:2`, `4:3`, `5:4`, `1:1`, `4:5`, `3:4`, `2:3`, `9:16`. |
| `resolution` | enum | no | `1K` | `0.5K` (drafts), `1K` (default), `2K` (final), `4K` (max). |
| `output_format` | enum | no | `png` | `png`, `jpeg`, `webp`. |
| `safety_tolerance` | int | no | 4 | 1 (strict) – 6 (permissive). |
| `limit_generations` | bool | no | true | Limit each prompt round to one generation. |
| `enable_web_search` | bool | no | false | Adds web grounding (extra cost + latency). |

For image edit (preserve subject + apply changes), see the sibling [`nano-banana-edit`](../nano-banana-edit) skill.

## How to invoke

**Default draft (1K, square, png):**

```bash
runcomfy run google/nano-banana-2/text-to-image \
  --input '{"prompt": "<user prompt>"}' \
  --output-dir <absolute/path>
```

**Vertical 4-up batch for ideation:**

```bash
runcomfy run google/nano-banana-2/text-to-image \
  --input '{
    "prompt": "<user prompt>",
    "num_images": 4,
    "aspect_ratio": "9:16",
    "resolution": "0.5K"
  }' \
  --output-dir <absolute/path>
```

**Final at 2K with seed lock:**

```bash
runcomfy run google/nano-banana-2/text-to-image \
  --input '{
    "prompt": "<user prompt>",
    "resolution": "2K",
    "aspect_ratio": "16:9",
    "seed": 42
  }' \
  --output-dir <absolute/path>
```

**Web-grounded (current event / real entity):**

```bash
runcomfy run google/nano-banana-2/text-to-image \
  --input '{
    "prompt": "<prompt referencing a real-world event from this week>",
    "enable_web_search": true
  }' \
  --output-dir <absolute/path>
```

## Prompting — what actually works

**Subject-first declarative grammar.** "A cinematic close-up portrait of an American woman standing under neon lights in rainy Tokyo, shallow depth of field, reflective wet streets, ultra-detailed, realistic skin texture" — primary subject, then action, environment, style, camera. Front-load subject; trail with directives.

**Exact text quoting for in-image typography.** "The label reads 'AURA' in clean bold sans-serif, centered, white on black" — quote the literal characters. Specify placement and font style. Don't say "with the brand name on it" and hope.

**Consistent seeds for refinement.** Lock `seed` when iterating a single prompt across small variants — keeps composition stable.

**Web-grounding, sparingly.** Turn on `enable_web_search` only when the prompt names current events / real entities. Adds latency + cost; off by default.

**Don't conflict styles.** "minimalist + ornate + retro + cyberpunk" cancels. Pick 1–2 anchors.

**Anti-patterns:**
- Trying to verbally describe a stable subject identity — use the **edit** endpoint with image refs instead.
- Asking for resolutions outside the 4 tiers → 422.
- Aspect ratios outside the 11 supported values → 422.
- Non-quoted in-image text → unpredictable rendering.

## Where it shines

| Use case | Why Nano Banana 2 |
|---|---|
| **Marketing draft thumbnails (batch of 4)** | Fast iteration at 0.5K, then promote winner to 2K |
| **Social-platform-native** | Wide aspect ratio support including 9:16, 4:5, 21:9 |
| **In-image typography for posters / cards** | Predictable text rendering when characters are quoted |
| **Web-grounded current-event imagery** | `enable_web_search` integrates fresh info |
| **Reproducible variant testing** | Strong seed + consistent framing |

## Sample prompts (verified to produce strong results)

**Cinematic portrait (page example):**

```
A cinematic close-up portrait of an American woman standing under neon
lights in rainy Tokyo, shallow depth of field, reflective wet streets,
ultra-detailed, realistic skin texture
```

**Brand-asset card with quoted text:**

```
A minimalist 16:9 product card: a matte black ceramic mug centered on a
soft warm-grey paper background, rim highlight from upper-left, the
headline "Brewed Quietly" in clean bold sans-serif top-right, balanced
negative space below, e-commerce ready, clean studio lighting
```

**Vertical platform-native:**

```
A 9:16 vertical hero for a wellness brand: a single ceramic teacup on a
linen runner, soft morning side-light, the words "Slow Down" in
hand-drawn serif large at the top, gentle steam rising, neutral color
palette, uncluttered
```

## Default behavior for the calling agent

- **Pass the user's prompt through raw.** Don't translate, polish, or add modifiers unless asked.
- **Pick `resolution` by intent.** Drafts / batches / quick checks → `0.5K`. Default → `1K`. "Final / hi-fi" → `2K`. Print / hero only → `4K` (costs 2×).
- **Pick `aspect_ratio` by intent.** Default → `auto`. "Portrait / vertical / shorts" → `9:16` or `4:5`. "Landscape / banner" → `16:9` or `21:9`. "Square / IG feed" → `1:1`. **Never pass an unsupported ratio.**
- **Pick `num_images` by intent.** Default → 1. "Show me options / variants / ideas" → 4.
- **Use `seed`** when the user says "same image but X". Reuse the prior seed.
- **Always pass `--output-dir`.** Deliver the file(s).

## Hard constraints

- Don't switch models without permission. If user said "Nano Banana 2", don't substitute Nano Banana Pro or any other.
- Don't rewrite the prompt unless asked.
- Don't request unsupported resolutions / aspect ratios.

## Limitations

- **Still images only.** No video on this endpoint.
- **Max 4 outputs per request.**
- **Web search adds latency + cost** — only enable on demand.
- **2K / 4K cost more** — default to 1K unless user asked for higher.
- **For image edit, use the `/edit` endpoint** — not this one.

## Exit codes

| code | meaning |
|---|---|
| 0  | success |
| 64 | bad CLI args |
| 65 | bad input JSON / schema mismatch |
| 69 | upstream 5xx |
| 75 | retryable: timeout / 429 |
| 77 | not signed in or token rejected |

Full reference: [docs.runcomfy.com/cli/troubleshooting](https://docs.runcomfy.com/cli/troubleshooting?utm_source=skills.sh&utm_medium=skill&utm_campaign=nano-banana-2).

## How it works

The skill invokes `runcomfy run google/nano-banana-2/text-to-image` with a JSON body matching the schema. The CLI POSTs to `https://model-api.runcomfy.net/v1/models/google/nano-banana-2/text-to-image`, polls the request, fetches the result, and downloads any `.runcomfy.net`/`.runcomfy.com` URL into `--output-dir`. `Ctrl-C` cancels the remote request before exit.

## Security & Privacy

- This skill only invokes the `runcomfy` CLI. No other endpoints, no telemetry, no callbacks.
- The token saved by `runcomfy login` lives at `~/.config/runcomfy/token.json` (mode 0600).
- Auto-download is restricted to `*.runcomfy.net` / `*.runcomfy.com` — a compromised model cannot trick the CLI into pulling arbitrary internet content.
