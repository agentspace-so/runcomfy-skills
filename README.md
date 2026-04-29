# runcomfy-skills

Agent skills for the [**RunComfy**](https://www.runcomfy.com/?utm_source=github&utm_medium=readme&utm_campaign=runcomfy-skills) Model API. Each skill teaches an AI agent how to call one specific model end-to-end via the [`runcomfy` CLI](https://docs.runcomfy.com/cli/introduction?utm_source=github&utm_medium=readme&utm_campaign=runcomfy-skills) — submit a request, wait for completion, download generated files.

## Skills in this repo

### Brand-name skills (9)

Pin a specific model. Triggers on the model brand name.

| Skill | Backs |
|---|---|
| [`gpt-image-2`](gpt-image-2/) | OpenAI **GPT Image 2** — text-to-image |
| [`gpt-image-edit`](gpt-image-edit/) | OpenAI **GPT Image 2 Edit** — multilingual text + multi-ref edit |
| [`flux-2-klein`](flux-2-klein/) | BFL **Flux 2 Klein** (9B / 4B) — sub-second iteration |
| [`flux-kontext`](flux-kontext/) | BFL **Flux 1 Kontext Pro** — single-shot precise local edit |
| [`happyhorse-1-0`](happyhorse-1-0/) | **HappyHorse 1.0** — Arena #1 video model (t2v + native audio) |
| [`nano-banana-2`](nano-banana-2/) | Google **Nano Banana 2** — flash-tier image generation |
| [`nano-banana-edit`](nano-banana-edit/) | Google **Nano Banana 2 Edit** — batch edit up to 20 images |
| [`seedance-v2`](seedance-v2/) | ByteDance **Seedance 2.0 Pro** — multi-modal cinematic video |
| [`wan-2-7`](wan-2-7/) | Wan-AI **Wan 2.7** — flagship video with audio-driven lip-sync |

### Intent-routed skills (3)

Smart routers — each picks the right model in the catalog based on user intent.

| Skill | Routes |
|---|---|
| [`image-to-video`](image-to-video/) | HappyHorse 1.0 I2V (default) · Wan 2.7 + `audio_url` (custom voiceover) · Seedance 2.0 Pro (multi-modal) |
| [`image-edit`](image-edit/) | Nano Banana Edit (default) · GPT Image 2 Edit (multilingual + multi-ref) · Flux Kontext (single-shot) · Z-Image Inpaint (mask-driven) |
| [`video-edit`](video-edit/) | Wan 2.7 Edit-Video (default) · Kling Motion-Control (motion transfer) · Lucy Edit Restyle (lightweight identity-stable) |

## Install one skill

```bash
npx skills add agentspace-so/runcomfy-skills --skill <slug> -g
```

## Prerequisite — the RunComfy CLI

All skills here call `runcomfy run …`. Install it once:

```bash
# Easiest
npm i -g @runcomfy/cli

# Or curl
curl -fsSL https://runcomfy.com/install.sh | sh
```

Then `runcomfy login` opens a browser device-code flow (free RunComfy account).

In CI / containers, set `RUNCOMFY_TOKEN=<token>` instead of `runcomfy login`.

## Security & Privacy

These skills only invoke the `runcomfy` CLI and read its output. No telemetry, no callbacks, no extra network endpoints. The CLI's auto-download of generated files is restricted to the `*.runcomfy.net` / `*.runcomfy.com` host whitelist, so a compromised model cannot trick the CLI into pulling arbitrary internet content.

## License

MIT — see [LICENSE](LICENSE).
