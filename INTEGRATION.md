# INTEGRATION — RoadDraw

> How this fork connects to the rest of BlackRoad OS

## Node Assignment

| Property | Value |
|----------|-------|
| **Primary Node** | Lucidia (.38) |
| **Fork Of** | Excalidraw |
| **RoundTrip Agent** | RoadDraw Agent |
| **NLP Intents** | 'draw diagram' / 'whiteboard' |
| **NATS Subject** | `blackroad.RoadDraw.>` |
| **GuardRail Monitor** | `https://guard.blackroad.io/status/RoadDraw` |

## Deployment

Deploy via blackroad-operator:

```bash
# From blackroad-operator
cd ~/blackroad-operator
./scripts/deploy/deploy-RoadDraw.sh

# Or via fleet coordinator
./fleet-coordinator.sh deploy RoadDraw

# Manual deploy to Lucidia (.38)
ssh blackroad@$(echo "Lucidia (.38)" | grep -oP '[0-9.]+' || echo "Lucidia (.38)") \
  "cd /opt/blackroad/RoadDraw && git pull && sudo systemctl restart RoadDraw"
```

## Systemd Service

```ini
[Unit]
Description=BlackRoad RoadDraw (Excalidraw fork)
After=network.target
Wants=network-online.target

[Service]
Type=simple
User=blackroad
WorkingDirectory=/opt/blackroad/RoadDraw
ExecStart=/opt/blackroad/RoadDraw/start.sh
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

## NATS Integration (CarPool)

```bash
# Subscribe to RoadDraw events
nats sub "blackroad.RoadDraw.>" --server nats://192.168.4.101:4222

# Publish status
nats pub "blackroad.RoadDraw.status" '{"node":"Lucidia (.38)","status":"running"}' \
  --server nats://192.168.4.101:4222
```

## RoundTrip Agent

The **RoadDraw Agent** manages this service via RoundTrip:

```bash
# Check agent status
curl -s https://roundtrip.blackroad.io/api/agents | jq '.[] | select(.name=="RoadDraw Agent")'

# Send command to agent
curl -X POST https://roundtrip.blackroad.io/api/chat \
  -H 'Content-Type: application/json' \
  -d '{"agent":"RoadDraw Agent","message":"status","channel":"fleet"}'
```

## GuardRail Monitoring

Add to Uptime Kuma (Alice :3001):

| Check | URL/Command | Interval |
|-------|------------|----------|
| HTTP Health | `http://Lucidia (.38):PORT/health` | 30s |
| Process | `systemctl is-active RoadDraw` | 60s |
| NATS Heartbeat | `blackroad.RoadDraw.heartbeat` | 60s |

## Memory System Integration

```bash
# Log actions
~/blackroad-operator/scripts/memory/memory-system.sh log deploy RoadDraw "Deployed to Lucidia (.38)"

# Add solutions to Codex
~/blackroad-operator/scripts/memory/memory-codex.sh add-solution "RoadDraw" "How to restart" \
  "sudo systemctl restart RoadDraw"

# Broadcast learnings
~/blackroad-operator/scripts/memory/memory-til-broadcast.sh broadcast "RoadDraw" "Config change: ..."
```

## Related Components

| Component | Role | Connection |
|-----------|------|-----------|
| **TollBooth** (WireGuard) | VPN mesh | All traffic between nodes |
| **CarPool** (NATS) | Messaging | Event pub/sub on `blackroad.RoadDraw.>` |
| **GuardRail** (Uptime Kuma) | Monitoring | Health checks every 30s |
| **RoadMem** (Mem0) | Memory | Persistent agent state |
| **OneWay** (Caddy) | TLS Edge | HTTPS termination on Gematria |
| **RearView** (Qdrant) | Vector Search | Semantic search over RoadDraw logs |
| **BackRoad** (Portainer) | Containers | Docker management if containerized |
| **PitStop** (Pi-hole) | DNS | Internal `RoadDraw.blackroad.local` resolution |
