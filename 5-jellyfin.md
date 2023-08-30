# Jellyfin
Reference: https://docs.linuxserver.io/images/docker-jellyfin

For Jellyfin to auto detect file changes:
```
# on the host system
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```

media files stored on usb drive at /mnt/usb1

- go into Portainer and create a Stack named jellyfin
- paste this into the editor. Adjust TZ, JELLYFIN_PublishedServerUrl, and volumes 

```
---
version: "2.1"
services:
  jellyfin:
    image: lscr.io/linuxserver/jellyfin:latest
    container_name: jellyfin
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/New_York
      - JELLYFIN_PublishedServerUrl=HOST-IP-ADDRESS #optional
    volumes:
      - /mnt/usb1/appdata/jellyfin:/config
      - /mnt/usb1/Shows:/data/tvshows
      - /mnt/usb1/Movies:/data/movies
      - /mnt/usb1/Music:/data/music
      - /mnt/usb1/Photos:/data/photos
      - /mnt/usb1/Other:/data/other
    ports:
      - 8096:8096
      - 8920:8920 #optional
      - 7359:7359/udp #optional
      - 1900:1900/udp #optional
    devices:
      - /dev/dri:/dev/dri # Intel GPU transcode
    restart: unless-stopped

networks:
  default:
    external: true
    name: rev_proxy
```
- deploy the stack
- connect to Jellyfin via web browser http://HOST-IP-ADDRESS:8096
- create the admin account
- navigate to dashboard-Server-Playback
- Hardware Acceleration: Intel QuickSync (QSV)
- Enable hardware decoding: check all of them
- Hardware encoding options: check all of them.
- Leave everything else at defaults
- go to Plugins
- install Kodi Sync Queue, TMDb Box sets, TheTVDB
- when you create a Jellyfin library for tv shows,
go in settings and move TheTVDB to top of each list.
- continue to configure Jellyfin. see https://jellyfin.org/docs/
