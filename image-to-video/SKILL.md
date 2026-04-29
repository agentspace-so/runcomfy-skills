---
name: image-to-video
displayName: "🎬 Image-to-Video — Pro Pack on RunComfy"
description: >
  Animate any still image on RunComfy — this skill is a smart router
  that matches the user's intent to the right i2v model in the
  RunComfy catalog. Picks HappyHorse 1.0 I2V (Arena #1, native audio,
  identity preservation) for general animations, Wan 2.7 with
  `audio_url` for custom-voiceover lip-sync, or Seedance 2.0 Pro for
  multi-modal animation from image + reference video + reference
  audio. Bundles each model's documented prompting patterns so the
  agent gets sharper output without burning iterations on the wrong
  model. Calls `runcomfy run <vendor>/<model>/image-to-video` (or
  endpoint variant) through the local RunComfy CLI. Triggers on
  "image to video", "image-to-video", "i2v", "animate image", "make
  this move", or any explicit ask to turn a still into video.
emoji: "🎬"
homepage: https://www.runcomfy.com
license: MIT
---

# 🎬 Image-to-Video — Pro Pack on RunComfy

[runcomfy.com](https://www.runcomfy.com/?utm_source=skills.sh&utm_medium=skill&utm_campaign=image-to-video) · [HappyHorse I2V](https://www.runcomfy.com/models/happyhorse/happyhorse-1-0/image-to-video?utm_source=skills.sh&utm_medium=skill&utm_campaign=image-to-video) · [Wan 2.7](https://www.runcomfy.com/models/wan-ai/wan-2-7/text-to-video?utm_source=skills.sh&utm_medium=skill&utm_campaign=image-to-video) · [Seedance 2.0 Pro](https://www.runcomfy.com/models/bytedance/seedance-v2/pro?utm_source=skills.sh&utm_medium=skill&utm_campaign=image-to-video) · [GitHub](https://github.com/agentspace-so/runcomfy-skills/tree/main/image-to-video)

**Image-to-video, intent-routed.** This skill doesn't lock you to one model — it picks the right i2v model in the RunComfy catalog based on what the user actually wants: portrait animation, custom-voiceover lip-sync, or multi-modal composition.

```bash
npx skills add agentspace-so/runcomfy-skills --skill image-to-video -g
```

## Pick the right model for the user's intent

| User intent | Model | Why |
|---|---|---|
| Animate a portrait — keep identity stable | **HappyHorse 1.0 I2V** | #1 on Artificial Analysis Arena (Elo 1392); strong facial fidelity |
| Product reveal / 360 / macro motion | **HappyHorse 1.0 I2V** | Geometry preservation + smooth camera moves |
| Native synchronized ambient audio in one pass | **HappyHorse 1.0 I2V** | In-pass audio synthesis |
| Animate **and** lip-sync to a **custom voiceover track** | **Wan 2.7 + `audio_url`** | Accepts your own MP3/WAV (3–30s, ≤15MB) and drives lip-sync to it |
| Multi-language dub variants (same image, different audio per call) | **Wan 2.7 + `audio_url`** | Same shot, swap `audio_url` per language |
| Multi-modal — image + reference video + reference audio together | **Seedance 2.0 Pro** | Up to 9 image refs, 3 video refs (2–15s each), 3 audio refs |
| Brand-consistent narrative with character ref + scene ref + voice ref | **Seedance 2.0 Pro** | Image holds identity, video holds scene, audio holds voice |
| Default if unspecified | **HappyHorse 1.0 I2V** | Best all-round quality + native audio |

The agent reads this table, classifies the user's intent, and picks the matching subsection below.

## Prerequisites

1. **RunComfy CLI** — `npm i -g @runcomfy/cli` or `curl -fsSL https://runcomfy.com/install.sh | sh`
2. **RunComfy account** — `runcomfy login` opens a browser device-code flow.
3. **CI / containers** — set `RUNCOMFY_TOKEN=<token>`.
4. **A source image URL** — JPEG/PNG/WebP, min 300px, ≤10MB; aspect 1:2.5 to 2.5:1 (HappyHorse) — other models have similar specs.

---

## Route 1: HappyHorse 1.0 I2V — default for portrait / product / general animation

**Model**: `happyhorse/happyhorse-1-0/image-to-video` · **Arena rank**: #1 (Elo 1392)

### Schema

| Field | Type | Required | Default | Notes |
|---|---|---|---|---|
| `image_url` | string | yes | — | JPEG/JPG/PNG/WEBP. Min 300px. Aspect 1:2.5–2.5:1. ≤10MB. |
| `prompt` | string | yes | — | ≤5000 non-CJK or 2500 CJK chars. **Motion / camera / lighting** description. |
| `resolution` | enum | no | `1080P` | `720P` or `1080P`. |
| `duration` | int | no | 5 | 3–15 seconds. |
| `seed` | int | no | 0 | Reuse for variant comparisons. |
| `watermark` | bool | no | true | Provider watermark toggle. |

Output aspect = input aspect. No independent reframing.

### Invoke

```bash
runcomfy run happyhorse/happyhorse-1-0/image-to-video \
  --input '{
    "image_url": "https://.../portrait.jpg",
    "prompt": "Gentle camera drift around the subject'\''s face, subtle breathing motion, identity-stable features, soft natural light."
  }' \
  --output-dir <absolute/path>
```

### Prompting tips

- **Lead with motion verbs**: "drift", "dolly in", "orbit", "tilt up", "reveal", "blink", "breathe". Front-load what's MOVING.
- **Don't restate the image** — the model sees it. Focus tokens on what changes.
- **Preservation goals explicit**: "identity-stable features", "packaging unchanged", "background geometry stable".
- **Lighting evolution**: "rim light intensifying", "shadows shortening as camera rises".
- **One beat per clip** — single primary motion (orbit OR dolly OR tilt OR character action).

---

## Route 2: Wan 2.7 + `audio_url` — when the user has a custom voiceover

**Model**: `wan-ai/wan-2-7/text-to-video` (NOT `/image-to-video` — Wan 2.7's t2v endpoint accepts an `audio_url` that drives lip-sync)

> **Note on i2v with Wan 2.7**: Wan 2.7's primary i2v animation isn't on a dedicated endpoint here. For pure i2v (image animated by motion prompt only), prefer **HappyHorse i2v**. Use Wan 2.7 specifically when the user has a custom audio track they want lip-synced to a generated talking-head clip.

### Schema (Wan 2.7 t2v with audio)

| Field | Type | Required | Default | Notes |
|---|---|---|---|---|
| `prompt` | string | yes | — | Up to ~5000 chars. Describe the talking-head shot: framing, lighting, motion. |
| `audio_url` | string | yes (for lip-sync) | — | WAV/MP3, 3–30s, ≤15MB. **Drives lip-sync.** |
| `aspect_ratio` | enum | no | `16:9` | `16:9`, `9:16`, `1:1`, `4:3`, `3:4`. |
| `resolution` | enum | no | `1080p` | `720p` or `1080p`. |
| `duration` | enum | no | `5` | 2–15 (whole seconds). Match your audio length. |
| `negative_prompt` | string | no | — | Concrete issues to avoid (e.g. "no subtitles, no flicker"). |
| `seed` | int | no | — | Reproducibility. |

### Invoke

```bash
runcomfy run wan-ai/wan-2-7/text-to-video \
  --input '{
    "prompt": "Medium close-up of a confident spokesperson in a softly-lit recording booth, leaning slightly toward the camera, locked tripod, shallow DOF, warm key light from camera-left.",
    "audio_url": "https://.../voiceover-en.mp3",
    "duration": 12,
    "aspect_ratio": "9:16"
  }' \
  --output-dir <absolute/path>
```

### Prompting tips

- **Describe the talking-head shot** — framing, lighting, lens feel. The audio drives the lip-sync; the prompt builds the visual frame around it.
- **Match `duration` to audio length** — clip will be silent past the audio if too long.
- **Use `negative_prompt` for issues**: `"no subtitles, no flicker, no distorted hands"`.
- **For multi-language dubs** — same prompt, swap `audio_url` per call. Lock seed for visual consistency across languages.

---

## Route 3: Seedance 2.0 Pro — multi-modal animation (image + ref video + ref audio)

**Model**: `bytedance/seedance-v2/pro`

Use when the user wants a single clip that combines: a **subject image** + **scene from a reference video** + **voice tone from a reference audio**.

### Schema (Seedance 2.0 Pro, i2v-relevant fields)

| Field | Type | Required | Default | Notes |
|---|---|---|---|---|
| `prompt` | string | yes | — | CN ≤500 chars OR EN ≤1000 words. |
| `image_url` | array | yes (for i2v) | `[]` | 0–9 images. **First is the primary subject.** |
| `video_url` | array | no | `[]` | 0–3 reference clips (MP4/MOV), 2–15s each. |
| `audio_url` | array | no | `[]` | 0–3 reference audio (WAV/MP3), 2–15s, < 15MB each. |
| `aspect_ratio` | enum | no | `adaptive` | `adaptive`, `16:9`, `9:16`, `4:3`, `3:4`, `1:1`, `21:9`. |
| `duration` | int | no | 5 | 4–15 (whole seconds). |
| `resolution` | enum | no | `720p` | `480p` or `720p`. |
| `generate_audio` | bool | no | true | In-pass synchronized speech / SFX / music. |
| `seed` | int | no | — | Reproducibility. |

### Invoke

```bash
runcomfy run bytedance/seedance-v2/pro \
  --input '{
    "prompt": "Subject from image 1 walks through the café in video 1, voice tone matches audio 1. Medium close-up, slow push-in, warm light, gentle ambience.",
    "image_url": ["https://.../subject.jpg"],
    "video_url": ["https://.../cafe-locked-shot.mp4"],
    "audio_url": ["https://.../voice-tone.mp3"],
    "duration": 8
  }' \
  --output-dir <absolute/path>
```

### Prompting tips

- **Image vs text division** — use `image_url` for what must stay stable (face, costume, brand); use `prompt` for what should evolve (action, mood, lighting).
- **Number the refs** in the prompt: `"subject from image 1, lighting from video 1, voice from audio 1"`. Seedance routes cues correctly.
- **Reference media specs** — videos / audio must be 2–15s; audio < 15MB.
- **Don't mix radically different aesthetics** — if image 1 is a watercolor and video 1 is photoreal, output drifts.

---

## Default behavior for the calling agent

1. **Classify intent.** Read the "Pick the right model" table. Match user's words to the row.
2. **Default to HappyHorse 1.0 I2V** if the intent isn't clear or just says "animate this image".
3. **Pass the user's prompt through raw** unless rewriting for the model's expected pattern (e.g. HappyHorse rewards motion-verb-leading; rewrite if needed).
4. **Always include preservation language** when the user cares about identity ("keep the face / brand / pose unchanged").
5. **Pick `duration` / `resolution` by intent.** Default `5s` / `1080P` (HappyHorse), `5s` / `1080p` (Wan), `5s` / `720p` (Seedance).
6. **Always pass `--output-dir`.** Deliver the file.

## Hard constraints

- Don't switch routes without permission. If user said "use Wan 2.7" / "with my voiceover", use Route 2; if user said "HappyHorse" or just "animate", use Route 1.
- Don't pass an audio track to HappyHorse — its audio is in-pass only. Use Wan 2.7 (Route 2) instead.
- Don't request resolutions / durations / aspect ratios outside each model's schema.

## Limitations

- **Each route inherits its model's limits.** HappyHorse: 15s cap, output aspect = input aspect. Wan 2.7: 15s cap, audio 3–30s/15MB. Seedance: 720p ceiling on this template, 15s cap.
- **No multi-route blending.** This skill picks one model per call. If the user wants HappyHorse animation + Wan-style lip-sync in the same clip, that's two calls + a stitch (out of scope here).
- **Brand-specific overrides** — if the user named a specific model variant not listed (e.g. Wan 2.6, Seedance 1.5), route to the corresponding brand skill (`wan-2-7`, `seedance-v2`) instead of forcing it through here.

## Exit codes

| code | meaning |
|---|---|
| 0  | success |
| 64 | bad CLI args |
| 65 | bad input JSON / schema mismatch |
| 69 | upstream 5xx |
| 75 | retryable: timeout / 429 |
| 77 | not signed in or token rejected |

Full reference: [docs.runcomfy.com/cli/troubleshooting](https://docs.runcomfy.com/cli/troubleshooting?utm_source=skills.sh&utm_medium=skill&utm_campaign=image-to-video).

## How it works

The skill picks one of HappyHorse 1.0 I2V / Wan 2.7 t2v+audio / Seedance 2.0 Pro based on user intent and invokes `runcomfy run <model_id>` with the matching JSON body. The CLI POSTs to the Model API, polls the request, fetches the result, and downloads any `.runcomfy.net`/`.runcomfy.com` URL into `--output-dir`. `Ctrl-C` cancels the remote request before exit.

## Security & Privacy

- This skill only invokes the `runcomfy` CLI. No other endpoints, no telemetry, no callbacks.
- The token saved by `runcomfy login` lives at `~/.config/runcomfy/token.json` (mode 0600).
- Auto-download is restricted to `*.runcomfy.net` / `*.runcomfy.com` — a compromised model cannot trick the CLI into pulling arbitrary internet content.
