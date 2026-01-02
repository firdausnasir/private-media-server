# Jellyfin + \*arr Stack

This stack turns your machine into a full home media server with automatic downloads, subtitles, requests, and remote access.

All services run independently using docker run — no docker-compose required.

## Folder Structure

All containers share these folders:

```
Configs: ~/.config/<service>
Media root: ~/Downloads/jellyfin_media
```

## Docker Compose

Copy [docker-compose.yml](docker-compose.yml) if don't want to run the services individually.

## Jellyfin — Media Streaming Server

Streams your movies & TV shows to browser, TV, mobile, etc.

```bash
docker run -d \
 --name jellyfin \
 --restart unless-stopped \
 -p 8096:8096 \
 -e TZ=Asia/Kuala_Lumpur \
 -v ~/.config/jellyfin/config:/config \
 -v ~/.config/jellyfin/cache:/cache \
 -v ~/Downloads/jellyfin_media/Movies:/media/movies \
 -v ~/Downloads/jellyfin_media/TV\ Shows:/media/tv \
 jellyfin/jellyfin:latest
```

Access: http://localhost:8096

## Jellyseerr — Request System for Family

Lets users request movies & TV shows.
Integrates with Sonarr / Radarr.

```
docker run -d \
 --name jellyseerr \
 --restart unless-stopped \
 -p 5055:5055 \
 -e TZ=Asia/Kuala_Lumpur \
 -v ~/.config/jellyseerr:/app/config \
 fallenbagel/jellyseerr:latest
```

Access: http://localhost:5055

## Sonarr — TV Series Automation

Automatically searches, downloads, renames and organizes TV shows.

```
docker run -d \
 --name sonarr \
 --restart unless-stopped \
 -p 8989:8989 \
 -e PUID=1000 \
 -e PGID=1000 \
 -e TZ=Asia/Kuala_Lumpur \
 -v ~/.config/sonarr:/config \
 -v ~/Downloads/jellyfin_media:/data \
 lscr.io/linuxserver/sonarr:latest
```

Access: http://localhost:8989

## Radarr — Movie Automation

Same as Sonarr but for movies.

```
docker run -d \
 --name radarr \
 --restart unless-stopped \
 -p 7878:7878 \
 -e PUID=1000 \
 -e PGID=1000 \
 -e TZ=Asia/Kuala_Lumpur \
 -v ~/.config/radarr:/config \
 -v ~/Downloads/jellyfin_media:/data \
 lscr.io/linuxserver/radarr:latest
```

Access: http://localhost:7878

## Prowlarr — Indexer Manager

Central manager for all torrent indexers.
Syncs automatically with Sonarr & Radarr.

```
docker run -d \
  --name prowlarr \
  --restart unless-stopped \
  -p 9696:9696 \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Asia/Kuala_Lumpur \
  -v ~/.config/prowlarr:/config \
  lscr.io/linuxserver/prowlarr:latest
```

Access: http://localhost:9696

## FlareSolverr — Cloudflare Bypass

Allows Jackett to scrape protected torrent sites.

```
docker run -d \
 --name flaresolverr \
 --restart unless-stopped \
 -p 8191:8191 \
 -e TZ=Asia/Kuala_Lumpur \
 -e LOG_LEVEL=info \
 ghcr.io/flaresolverr/flaresolverr:latest
```

Access: http://localhost:8191

## qBittorrent — Torrent Client

Handles the actual downloading.

```
docker run -d \
 --name qbittorrent \
 --restart unless-stopped \
 -p 8080:8080 \
 -p 6881:6881 \
 -p 6881:6881/udp \
 -e PUID=1000 \
 -e PGID=1000 \
 -e TZ=Asia/Kuala_Lumpur \
 -e WEBUI_PORT=8080 \
 -e WEBUI_USERNAME=admin \
 -e WEBUI_PASSWORD=admin \
 -v ~/.config/qbittorrent:/config \
 -v ~/Downloads/jellyfin_media:/data \
 lscr.io/linuxserver/qbittorrent:latest
```

Access: http://localhost:8080

## Bazarr — Subtitle Automation

Automatically downloads subtitles for your movies & TV shows.

```
docker run -d \
 --name bazarr \
 --restart unless-stopped \
 -p 6767:6767 \
 -e PUID=1000 \
 -e PGID=1000 \
 -e TZ=Asia/Kuala_Lumpur \
 -v ~/.config/bazarr:/config \
 -v ~/Downloads/jellyfin_media:/data \
 lscr.io/linuxserver/bazarr:latest
```

Access: http://localhost:6767

## Cloudflared — Remote Access Tunnel

Exposes Jellyfin securely without port forwarding.

```
docker run -d \
 --name cloudflared \
 --restart unless-stopped \
 cloudflare/cloudflared:latest \
 tunnel --no-autoupdate run --token YOUR_TOKEN_HERE
```

## Recommended Start Order

1. qBittorrent
2. Jackett + FlareSolverr
3. Sonarr / Radarr
4. Bazarr
5. Jellyfin
6. Jellyseerr
7. Cloudflared
