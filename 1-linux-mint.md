# Install Linux Mint
see https://linuxmint-installation-guide.readthedocs.io/en/latest/index.html

- I prefer to do this step with a monitor rather than a TV.
- go into BIOS and **disable Secure Boot.**
- Install Linux Mint in the usual manner
- You can use either wired or wireless connection during installation
- I chose "Erase disk and install Linux Mint". I'm not going to run Windows on this unit

After installation, remove the usb stick, make sure it boots into Linux Mint.

- Update the system with the gui update manager tool or `sudo apt update && sudo apt upgrade`

## ssh and x11vnc
For remote administration. You can use a VNC client such as Remmina

```
sudo apt install openssh-server x11vnc
```

## configure x11vnc
Create a VNC password file.
```
sudo x11vnc -storepasswd yourVNCpasswordHERE /etc/x11vnc.pass
```
Create and edit `/etc/systemd/system/x11vnc.service`
```
sudo nano /etc/systemd/system/x11vnc.service
```
Put this in the file
```
[Unit]
Description=Start x11vnc at startup.
After=multi-user.target

[Service]
Type=simple
ExecStart=/usr/bin/x11vnc -xkb -auth guess -forever -loop -noxdamage -repeat -rfbauth /etc/x11vnc.pass -rfbport 5900 -shared

[Install]
WantedBy=multi-user.target
```
Enable the service:
```
sudo systemctl enable x11vnc.service
sudo systemctl daemon-reload
```

## Static IP address
- configure a static IP address for the ethernet port, using gui tool.
- you can use 9.9.9.9 for DNS server if you're not sure.
- disable wireless
