# Kodi
Kodi is optional, you can use a web browser or Jellyfin media player instead.
Kodi interface is easier to use without a mouse.

https://kodi.wiki/view/HOW-TO:Install_Kodi_for_Linux#Installing_Kodi_on_Ubuntu-based_distributions_with_Team_Kodi_PPA

```
sudo apt install software-properties-common
sudo add-apt-repository -y ppa:team-xbmc/ppa
sudo apt install kodi
```

## Settings
on Kodi home page, go to the "gear"

### System/Input
- Enable mouse and touch screen support - Off

### Interface/Skin/Configure Skin/Main menu items
- uncheck the stuff you don't want to appear on the Kodi home page

### System/Add-ons
- Unknown sources - On

### System/Display
- Display mode - Full Screen
- Resolution - 1920x1080p

### Player/Videos
- Adjust display refresh rate - On start/stop
- Sync playback to display - On

## Jellyfin for Kodi Add-on
- see: https://jellyfin.org/docs/general/clients/kodi
- choose "add-on mode"
- If you have a large Jellyfin library, it will take a LONG time to sync to Kodi

## Main screen got changed
- Problem: Jellyfin changes the main screen for movies and tv.
- It doesn't show "Categories" on the top row any more.
- to revert, go to 'the gear' on the home page
- interface → skin → configure skin → main menu items → edit categories (underneath Movies)
- it wants to install "library editor", let it
- you want to "reset library nodes to default"
- Also do this for "edit categories" under TV Shows
- Restart Kodi. It should look like it did before

## Volume "normalization" - optional
Problem: Some content has too much "dynamic range", with whisper soft dialog,
and way too loud explosions and stuff. Kodi can limit the dynamic range, somewhat.
See here: https://www.reddit.com/r/kodi/comments/421gf7/how_to_normalize_audio_in_kodi/

- play a video, and while it's playing hit "m".
- go over to the "gear" and select Audio settings
- the idea is to reduce "Audio" and increase "Audio Amplification"
- Try Audio -18db and Audio Amplification +12db as a starting point.
- Unfortunately, you have to play with the settings to get it right.
- Too much "Audio Amplification", and you get distortion or excessive "pumping"
- When you have the settings the way you want, you can "set as default for all media"

## Autostart Kodi on login
- copy the kodi.desktop file into the autostart directory
```
cp /usr/share/applications/kodi.desktop ~/.config/autostart/
```
Note:
- I occasionally use Firefox (with uBlock Origin) to watch Youtube and other similar sites.
- So I set Firefox to Autostart, in the same way, by copying the firefox.desktop file.
- However, this makes Firefox displayed on the TV upon login. I want it to be Kodi
- So I had to add a delay for Kodi autostart
- Go in the gui "Startup Applications"
- Find Kodi, and click Edit.
- Add a 5 second delay

This ensures Kodi starts after Firefox, and is displayed on the TV when you login.
- to get to Firefox, Alt-Tab
- to make youtube video fullscreen, press "f", press "f" again to go back
- to go back to Kodi, Alt-Tab
- to launch another application, press the "windows" key and the Mint menu appears
- to toggle Kodi between full-screen and "window mode", press \ within Kodi

