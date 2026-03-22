# RoadDraw — Sovereign Whiteboard

> Forked from [Excalidraw](https://github.com/excalidraw/excalidraw). Visual collaboration for BlackRoad agents and humans.

**RoadDraw** is a collaborative whiteboard that runs on your infrastructure. Draw, diagram, brainstorm — with AI agents that can see and contribute to your canvas.

## Features
- **Real-time collaboration** via WebSocket on BlackRoad Mesh
- **AI-assisted drawing** — agents can generate diagrams from text
- **Self-hosted** — no Excalidraw servers, everything on your Pi fleet
- **Export** — PNG, SVG, JSON
- **Offline-first** — works without internet

## Deploy
```bash
npm install && npm run build
# Deploy to Caddy on Gematria
scp -r build/ gematria:/var/www/draw.blackroad.io/
```

---
© 2026 BlackRoad OS, Inc. Fork of Excalidraw (MIT). BlackRoad customizations proprietary.
