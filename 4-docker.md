# Docker - Portainer
In this example, Docker Persistent data (appdata) is stored on usb drive mounted at /mnt/usb1

## Install
```
# First, some prerequisites.
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$UBUNTU_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Note: If using Ubuntu it's "$VERSION_CODENAME". Linux Mint, use "$UBUNTU_CODENAME"

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## Test Docker
`sudo docker run hello-world`

## Allow user to run Docker
- `sudo usermod -aG docker $USER`
- Log out and log in to take effect.

## Ensure Docker waits for usb drive to spin up
- Since docker "appdata" is stored on "spinning rust" usb drive,
it's possible that docker will start before the drive is mounted.
- If this happens, docker stuff won't work.

### Identify the usb drive mounted at /mnt/usb1
`systemctl list-units --type=mount | grep /mnt | awk '{ print $1 }' | grep -v overlay2`

Result: mnt-usb1.mount

### edit the service file
```
sudo systemctl edit docker.service

[Unit]
After=local-fs.target mnt-usb1.mount
```
- This makes docker wait for usb disk to be mounted.
- It creates /etc/systemd/system/docker.service.d/override.conf
- This setting will be preserved when the system is updated.

## Portainer

Note: we add port 9000 so we can access via http rather than https.

```
docker volume create portainer_data
docker run -d -p 8000:8000 -p 9443:9443 -p 9000:9000 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest

# Verify
docker ps
# CONTAINER ID   IMAGE                          COMMAND                  CREATED       STATUS      PORTS                                                                                  NAMES             
# de6b38eb2fa9   portainer/portainer-ce:latest  "/portainer"             2 weeks ago   Up 9 days   0.0.0.0:8000->8000/tcp, :::8000->8000/tcp, 0.0.0.0:9443->9443/tcp, :::9443->9443/tcp   portainer
```

- Portainer wants a 12 char password.
- give it one, log in, go to Settings...Authentication...Move slider to 8 characters
- Then, can go to Users...admin...Change user password

## Reverse Proxy Network
- Need this if you want Jellyfin accessible from internet.
- In Portainer, go to Networks and add new network `reverse_proxy`, with default settings

## Containers
These are the containers we use.
- jellyfin: Media Server
- swag: https/ssl certifcate via LetsEncrypt
- duckdns: DNS for "dynamic" external IP address.
Not needed if you already have a domain name and dynamic DNS set up

