# picoclaw-terminal

DevWorkspace definition for running PicoClaw on Eclipse Che.
PicoClaw is an open-source personal AI assistant (Go, MIT).
Upstream: https://github.com/sipeed/picoclaw

## Architecture

- `devfile.yaml` — workspace: picoclaw launcher container + Service + Route (OpenShift)
- `.che/che-editor.yaml` — ttyd terminal editor (container-contribution pattern)
- Image: `docker.io/sipeed/picoclaw:launcher` (web UI + gateway, official image)
- ttyd image: `docker.io/tsl0922/ttyd:1.7.7-alpine` (injected by editor, not baked in)

## Ports

| Port  | Service            | Exposure |
|-------|--------------------|----------|
| 18800 | PicoClaw web UI    | public (Route, type: main) |
| 18790 | Gateway / webhooks | internal |
| 7681  | ttyd terminal      | public (Che Dashboard URL) |

## Config

- Config persists in PVC at `/home/user/.picoclaw/config.json`
- Survives restarts, lost on workspace deletion
- `PICOCLAW_GATEWAY_HOST=0.0.0.0` is required for the gateway to bind on all interfaces
- First boot: run `picoclaw onboard` in the terminal, then restart the workspace

## Common oc commands

```bash
# Get pod name
oc get pods -n <namespace> -l controller.devfile.io/devworkspace_name=picoclaw-terminal -o jsonpath='{.items[0].metadata.name}'

# Get web UI Route URL
oc get route picoclaw-terminal -n <namespace> -o jsonpath='https://{.spec.host}/'

# Restart workspace
oc patch devworkspace picoclaw-terminal -n <namespace> --type=merge -p '{"spec":{"started":true}}'
```

## Key differences from zeroclaw-terminal

- No custom Dockerfile — uses the official upstream launcher image directly
- Web UI is built into the launcher (port 18800) — primary interface
- ttyd shell uses `/bin/sh` (not bash — launcher image is Alpine-based)
- Config is JSON (`config.json`), not TOML
