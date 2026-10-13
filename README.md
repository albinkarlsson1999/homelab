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

## Deployment (GitOps)
This repo is the single source of truth. Every docker service runs as a **Portainer stack deployed directly from this repository**:

1. Change a compose file and push to `main`
2. Portainer pulls the change and redeploys the stack

Compose files cant be edited inside Portainer, all changes go through Git, so whats in this repo is always what is running. Portainer itself is the expection and runs from its own compose file

## Remote access 
- **Cloudflare Tunnel** exposes Home Assistant and other selected services via Zero Trust without the need of opening ports on my router
- **Wireguard** for full access to my home network when I am away
- **PostgreSQL** exposes no ports at all — it's only reachable through its own dedicated tunnel

## Secrets
No passwords or keys are committed. Secrets are referenced as `${VARIABLES}` in the compose files and set as **stack environment variables in Portainer**. Each service that needs secrets has a `.env.example` listing the required variables.

The .gitignore uses a **whitelist** — everything is ignored by default and only
compose files and docs are allowed in — so nothing sensitive can slip through by accident.

## Running a service manually
Without Portainer, any service can be started with plain Docker Compose:

    cd <service>
    cp .env.example .env   # fill in the values
    docker compose up -d
