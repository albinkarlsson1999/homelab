## Hardware
- **Raspberry Pi 5** (4 GB RAM), booting from a 128 GB M.2 SATA SSD instead of an SD card for reliability
- **UniFi Cloud Gateway** — routing, firewall and VLANs

## Network
The network is segmented into VLANs to keep untrusted devices away from the rest.

| VLAN | Purpose | Notes |
|---|---|---|
| Main | Trusted devices | Laptops, phones |
| IoT | Smart home devices | ESP32s, smart plugs/lamps, 3D printer — isolated from Main |
| Servers | Raspberry Pi | Reachable from Main, and from specific IoT devices so sensors can report to Home Assistant |

## Services
| Service | What it does | Runs in |
|---|---|---|
| Home Assistant | Smart home hub - sensors, lights, 3D printer monitoring | Docker |
| Pi-hole | Network-wide ad and tracker blocking (DNS) | Docker |
| Jellyfin | Media Server | Docker |
| Homepage | Dashboard for all services with links | Docker |
| PostgreSQL | Database for own projects | Docker |
| Portainer | Web UI for managing docker containers | Docker |
| Cloudflared | Cloudflare Tunnel for remote access | Docker |
| Wireguard | VPN into home network | Native |
| Minecraft server | Modded Forge (Feed The Beast) server | Native |

## Remote access 
- **Cloudflare Tunnel** exposes Home Assistant and other selected services via Zero Trust without the need of opening ports on my router
- **Wireguard** for full access to my home network when I am away

## Secrets
No passwords or keys are commited. Each service reads its secrets from a local .env file (git-ignored). Copy the example and fill it in:

```bash
cp .env.example .env
```

The .gitignore uses a **whitelist** — everything is ignored by default and only
compose files and docs are allowed in — so nothing sensitive can slip through by accident.

## Running a service
```bash
cd <service>
docker compose up -d
```
