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

Note:  
Once the box was set up the way I wanted,  
I decided I didn't want x11vnc running all the time.  
Kodi changes the display settings to best match the currently playing video,  
and this causes problems with x11vnc.  
I stopped and disabled x11vnc from running at boot:  
```
sudo systemctl stop x11vnc.service 
sudo systemctl disable x11vnc.service
sudo systemctl daemon-reload
```

To start x11vnc:
- make sure you're **not playing a video in Kodi**. Ok if Kodi is just showing the menu.
- ssh into the the device and run `sudo systemctl start x11vnc`
- connect with vnc client, do what you need to do.
- **Do not** start playing a video in Kodi
- close the vnc client and run `sudo systemctl stop x11vnc`
- how to check x11vnc status: `systemctl status x11vnc`

## Static IP address
- configure a static IP address for the ethernet port, using gui tool.
- you can use 9.9.9.9 for DNS server if you're not sure.
- disable wireless
