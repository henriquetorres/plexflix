# PlexFlix

This is my personal Mediaserver installation, based on [Dockerized](http://docker.com) 
apps. 

### Applications (and their respective docker projects):

- Plex - https://github.com/linuxserver/docker-plex
- Sonarr - https://github.com/linuxserver/docker-sonarr
- Radarr - https://github.com/linuxserver/docker-radarr
- Tautulli - https://github.com/linuxserver/docker-tautulli
- Ombi - https://github.com/linuxserver/docker-ombi
- Jackett - https://github.com/linuxserver/docker-jackett
- Caddy - https://github.com/lucaslorentz/caddy-docker-proxy
- Rclone - https://github.com/Mumie-hub/docker-services/tree/master/rclone-mount
- Transmission (+VPN) - https://github.com/haugene/docker-transmission-openvpn
- Borgmatic - https://github.com/b3vis/docker-borgmatic
- Watchtower - https://github.com/containrrr/watchtower
- Portainer - https://hub.docker.com/r/portainer/portainer

### Features:
- Integrated solution, with automatic download for TV shows and movies
- Google Drive integration, for "unlimited" storage in the cloud (encrypted)
- VPN integration for protected torrent downloading
- Automatic SSL (https) certificates generation
- Automatic daily backups using borg (only for configuration, as the media will be saved in Google Drive)
- Automatic update of docker images using watchtower

### Requirements:
- VPN account
- Google Drive account

# Initial Configuration

## Configure your environment

- Create a `.env` file with your configuration (see `.env.sample`)
- Create a copy of `defaults` folder called `config`
- Create borgmatic configurtions in `config/borgmatic/config.yml` (see the samples)

## Plex
To be able to configure plex for the first time, add the following to the `docker-compose.override.yml`:
```
---
version: '3.7'
services:
  plex:
    network_mode: host
```
Remove this after the plex server is properly configured and claimed, or else the other apps 
won't be able to communicate with it

## Transmission + VPN
See https://github.com/haugene/docker-transmission-openvpn for details on how to configure the VPN 
access. If you are using a custom VPN, copy your VPN Config to `/config/vpn`

## Borgmatic (backup
Before borgmatic can do its magic, you need to create a new repository. Make sure to set your 
password in `/config/borgmatic/config.yml` first (see "Configure your environment" above)

Command to initialize a new repo:
```
docker-compose run borgmatic sh -c "borgmatic --init --encryption repokey-blake2"
```

## Other apps
Most applications need to be configured before being able to be properly proxied (ex: add base path).
To be able to do it, create a `docker-compose.override.yml` exposing the ports for the app. Ex:
```
---
version: '3.7'
services:
  jackett:
    ports:
    - 9117:9117
```

After starting the container, you should be able to go to http://your.domain.com:9117 and configure Jackett
to the correct base path `/jackett`

Apps that require this workaround, and their respective ports that need to be open:
- Sonarr: 8989
- Radarr: 7878
- Tautulli: 8181
- Jackett: 9117
- Ombi: 3579

Remember to remove this after the app is properly configured, as the ports will be exposed to 
external access

# To Do
- Log rotate https://hub.docker.com/r/blacklabelops/logrotate
- Auto clean .trash (remove older than 1 month)

# Future
- Calibre https://hub.docker.com/r/linuxserver/calibre-web/
- Cockpit https://cockpit-project.org/