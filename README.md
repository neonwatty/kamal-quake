# kamal-quake [![Discord](https://img.shields.io/badge/Discord-Join%20Server-7289da?style=flat&logo=discord&logoColor=white)](https://discord.gg/nkeZbsc4gV)

A simple recipe for self-hosting [quakejs](https://github.com/inolen/quakejs) with **HTTPS and multiplayer support** using [Kamal 2](https://kamal-deploy.org/). Kamal handles the reverse proxy, SSL certificates via Let's Encrypt, and Docker deployment automatically.  [Join us on Discord](https://discord.gg/nkeZbsc4gV) to discuss further improvements, new features, and game times!

<p align="center">
<img align="center" src="https://github.com/jermwatt/readme_gifs/blob/main/kamal-quake-demo.webp" height="325">
</p>

## Quick Start

### 1. Prerequisites

- A VPS with at least 1GB RAM
- A domain pointing to your VPS IP
- Docker registry (GitHub Container Registry, Docker Hub, etc.)
- Ruby installed (`gem install kamal`)

### 2. VPS Setup

SSH into your VPS and configure Docker permissions:

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
```

Open these firewall ports:
- **443** - HTTPS traffic
- **27961** - WebSocket multiplayer

### 3. Configure

Clone this repo and edit `config/deploy.yml`:

```yaml
servers:
  web:
    hosts:
      - YOUR_VPS_IP           # Your server IP

proxy:
  host: your-domain.xyz       # Your domain

env:
  SERVER: your-domain.xyz     # Same domain

ssh:
  keys:
    - ~/.ssh/your-key.pem     # Your SSH key path
```

Set your registry token in `.kamal/secrets`:

```bash
GITHUB_REGISTRY_TOKEN=$(cat .env)  # Or your token directly
```

### 4. Deploy

```bash
kamal setup
```

Visit `https://your-domain.xyz` to play!

## Architecture

```
Browser → HTTPS (443) → kamal-proxy → Apache (80) → Game assets
Browser → WSS (27961) → wssproxy → Game server (27960)
```

Kamal-proxy handles SSL termination. The container runs a WSS proxy on port 27961 that terminates TLS using the same certificates and forwards to the internal game server.

## Multiplayer

### Starting a Game

1. Visit your URL - you'll see the asset download screen
2. Once loaded, press `Escape` to open the menu
3. Navigate to `Multiplayer` → Create a game

### Joining a Game

New players are automatically connected to active games. If not, press `Escape` → `Multiplayer` → Select the game.

## Troubleshooting

**Stuck on loading screen?** Check firewall ports 443 and 27961 are open.

**SSL errors?** Ensure your domain DNS is properly configured and pointing to your VPS IP.

**Multiplayer not working?** The WSS proxy starts automatically when SSL certificates are detected. Check container logs with `kamal app logs`.
