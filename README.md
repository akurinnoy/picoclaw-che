# PicoClaw Terminal — DevWorkspace with Web Terminal

Run [PicoClaw](https://github.com/sipeed/picoclaw) on Eclipse Che with a browser-based terminal and web UI.

PicoClaw is an open-source personal AI assistant written in Go. It runs as a lightweight daemon (<10MB RAM) and connects to 30+ LLM providers and 16+ messaging channels.

## Architecture

Two interfaces, one container:

- **Web UI** (port 18800) — configure agents, channels, scheduled tasks
- **ttyd terminal** (port 7681) — browser shell for CLI access and `picoclaw onboard`
- **Gateway** (port 18790, internal) — webhook endpoint for messaging channel integrations

## Quick Start

### 1. Open in Eclipse Che

```
https://<che-host>#https://github.com/<org>/picoclaw-terminal
```

### 2. Run `picoclaw onboard`

The Dashboard opens a browser terminal. Configure PicoClaw:

```sh
picoclaw onboard
```

Follow the interactive prompts to set your LLM provider and API key.

### 3. Restart the workspace

The launcher reads config only at startup. Restart from the Che Dashboard to apply changes.

### 4. Access the web UI

The web UI Route URL is shown in the Che Dashboard, or get it manually:

```sh
oc get route picoclaw-terminal -n <namespace> -o jsonpath='https://{.spec.host}/'
```

## LLM Providers

PicoClaw supports 30+ providers out of the box including:

- **OpenRouter** — access many models with one API key (recommended)
- Anthropic, OpenAI, Gemini, DeepSeek, Groq, Ollama, and more

## File Structure

```
.
├── .che/
│   └── che-editor.yaml   # ttyd terminal editor (container-contribution)
├── devfile.yaml           # workspace definition
├── CLAUDE.md              # context for Claude Code sessions
└── README.md
```
