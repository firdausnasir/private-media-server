# Jellyfin + \*arr Stack

This stack turns your machine into a full home media server with automatic downloads, subtitles, requests, and remote access.

All services run independently using docker run — no docker-compose required.

## Installation

### Make directory for config

```bash
mkdir -p ~/.config/{jellyfin,jellyseerr,sonarr,radarr,prowlarr,bazarr,flaresolverr,qbittorrent}
```

### Create media folder

```bash
mkdir -p ~/jellyfin/media
```

### Docker compose
Copy [docker-compose.yml](docker-compose.yml) into `~/jellyfin/media` folder

### Setup .env file
Copy .env file and change the value as needed
```bash
cp .env.example .env
```

### Run docker compose
```bash
docker compose up -d
```