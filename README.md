<div align="center">

# OpenClaw

**Your own personal AI assistant. Any OS. Any platform. The lobster way.**

A multi-channel AI gateway that runs on your devices, talks across your favorite messaging apps, and turns natural-language requests into real actions on your computer.

[![Node](https://img.shields.io/badge/Node-24-339933?logo=node.js&logoColor=white)](https://nodejs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![pnpm](https://img.shields.io/badge/pnpm-workspace-F69220?logo=pnpm&logoColor=white)](https://pnpm.io)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-macOS%20%7C%20Linux%20%7C%20Windows%20%7C%20iOS%20%7C%20Android-lightgrey)]()

</div>

---

## Overview

OpenClaw is a personal, single-user AI assistant designed to feel local, fast, and always-on. Instead of being a single chat app, it is a **gateway** — a small daemon you run yourself — that connects your AI agent of choice to the tools, devices, and channels you already use every day.

You message it on WhatsApp, Telegram, Slack, Discord, iMessage, Signal, Matrix (and many more). It can speak and listen on macOS, iOS, and Android, render an interactive Canvas UI, and execute real actions through a sandboxed agent harness with strong defaults around security and approvals.

This repository is a personal fork/mirror used by [Mikael David](https://github.com/MikaelDDavidd) for tracking, experiments, and integrations.

### Key Features

- **Multi-channel messaging** — 25+ channels out of the box: WhatsApp, Telegram, Slack, Discord, iMessage, BlueBubbles, Signal, Matrix, Microsoft Teams, IRC, Feishu, LINE, Mattermost, Nextcloud Talk, Nostr, Zalo, WeChat, WebChat, and more.
- **Pluggable model providers** — OpenAI, Anthropic, Google, DeepSeek, Mistral, Groq, Hugging Face, Cloudflare AI Gateway, Bedrock, Vertex, GitHub Copilot, and a long tail of self-hosted/custom backends.
- **Agent harness** — Tool use, computer use, browser automation, image generation, and speech (TTS/STT via Deepgram, ElevenLabs, Whisper, etc.).
- **Companion apps** — Native shells for macOS, iOS, and Android plus a web UI for the Canvas surface.
- **Plugin SDK** — Extensive `plugin-sdk` (50+ runtime modules) for building your own channels, providers, tools, and skills.
- **Skills system** — Drop-in capabilities published via ClawHub or loaded locally.
- **Security-first defaults** — Sandboxed execution (Docker / Podman), explicit approvals, secret scanning baseline, SSRF runtime, and a documented security policy.
- **CLI-first onboarding** — `openclaw onboard` walks you through gateway setup, channel pairing, and skill installation.

## Tech Stack

- **Language**: TypeScript (Node.js 24, ESM, strict mode)
- **Workspace**: pnpm monorepo (`apps/`, `packages/`, `extensions/`, `ui/`, `skills/`)
- **Build**: tsdown + custom build pipeline (`scripts/tsdown-build.mjs`)
- **Test**: Vitest (unit, e2e, contracts, channels, performance, gateway, extensions)
- **Lint / Format**: oxlint, oxfmt, swiftlint/swiftformat (for native shells), markdownlint
- **Native**: Swift (macOS/iOS — `apps/macos`, `apps/ios`, `Swabble/`), Kotlin/Java (`apps/android`)
- **Frontend (Canvas / UI)**: Vite + the `ui/` package
- **Containers**: Docker + dedicated sandbox images (`Dockerfile.sandbox`, `Dockerfile.sandbox-browser`)
- **Deploy targets**: Fly.io (`fly.toml`), Render (`render.yaml`)

## Getting Started

### Prerequisites

- **Node.js 24** (recommended) or 22.16+
- **pnpm** (preferred) — `npm install -g pnpm`
- **Docker** or **Podman** (for sandboxed agent execution)
- macOS, Linux, or Windows via WSL2

### Install (recommended global CLI)

```bash
npm install -g openclaw@latest
# or: pnpm add -g openclaw@latest

openclaw onboard --install-daemon
```

Onboarding installs the Gateway as a user service (`launchd` on macOS, `systemd` on Linux) so it stays running in the background.

### From source (development)

```bash
git clone git@gitlab.com:mikaeldavidlopes/openclaw.git
cd openclaw

pnpm install
pnpm build

# Run the gateway from source
pnpm dev -- gateway --port 18789 --verbose
```

### Quick start

```bash
# Start the gateway
openclaw gateway --port 18789 --verbose

# Send a message through any connected channel
openclaw message send --to +1234567890 --message "Hello from OpenClaw"

# Talk to the assistant
openclaw agent --message "Plan my week" --thinking high
```

For the full beginner path (auth, channel pairing, skills): see the upstream [Getting Started guide](https://docs.openclaw.ai/start/getting-started).

## Project Structure

```
openclaw/
├── src/                  # Gateway core (TypeScript)
├── apps/
│   ├── macos/            # Native macOS companion app (Swift)
│   ├── ios/              # Native iOS companion app (Swift)
│   ├── android/          # Native Android companion app
│   └── shared/           # Shared native code
├── packages/
│   ├── clawdbot/         # Bot runtime
│   ├── memory-host-sdk/  # Memory plugin host SDK
│   └── moltbot/          # Auxiliary bot package
├── extensions/           # 89+ first-party extensions (channels, providers, tools)
│   ├── anthropic, openai, google, deepseek, ...   # Model providers
│   ├── discord, slack, imessage, matrix, ...      # Channels
│   └── browser, github, notion, obsidian, ...     # Tools / integrations
├── skills/               # Bundled skills (ClawHub-compatible)
├── ui/                   # Web Canvas UI (Vite)
├── docs/                 # Full documentation site source
├── scripts/              # 170+ build, test, codegen, and ops scripts
├── test/                 # Cross-cutting test suites
└── openclaw.mjs          # CLI entry point
```

## Plugin SDK

OpenClaw exposes a rich plugin SDK (see `package.json` exports under `./plugin-sdk/*`). It is split into focused runtime modules — channel, agent, speech, media, security, gateway, setup, approval, reply, conversation, infra, SSRF, and more — so plugins only depend on the surfaces they actually need.

Recommended distribution path: publish your plugin as its own npm package and load it locally during development. See `docs/tools/plugin.md` and the upstream [plugin docs](https://docs.openclaw.ai).

## Security

OpenClaw treats security as a deliberate tradeoff: strong defaults without killing capability. Highlights:

- Sandboxed agent execution via Docker/Podman.
- Detect-secrets baseline (`.secrets.baseline`) and `.detect-secrets.cfg` enforced via pre-commit.
- Dedicated SSRF and security runtimes in the plugin SDK.
- `zizmor` workflow auditing for GitHub Actions.

Reporting vulnerabilities and the full policy: see [SECURITY.md](SECURITY.md).

## Contributing

This is a personal fork. For upstream contributions, please read [CONTRIBUTING.md](CONTRIBUTING.md) and follow the rules in [VISION.md](VISION.md):

- One PR = one issue/topic.
- Avoid PRs over ~5,000 changed lines.
- Prefer plugins/skills over core changes.

## License

Distributed under the MIT License. See [LICENSE](LICENSE) for details.

---

<div align="center">

Maintained fork by <a href="https://github.com/MikaelDDavidd">Mikael David</a> · Original project: <a href="https://github.com/openclaw/openclaw">openclaw/openclaw</a>

</div>
