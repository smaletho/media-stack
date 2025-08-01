## Overview

This is a media stack that I use to host Plex and "Arr" apps for TV shows and movies. Necessary traffic is routed through the VPN stack for privacy.

I run this on an Intel NUC PC running Ubuntu 24.04.

## VPN Stack

I use NordVPN, but I'm sure this is easily adaptable to other VPN services.

All traffic from containers with `network_mode: "container:shared-vpn"` will funnel through this VPN. 

You can verify this is working properly by getting your public IP from within the container. Run `curl ifconfig.me` from within one of the containers that should be utilizing the VPN, then plug that IP address into [NordVPN IP Lookup](https://nordvpn.com/ip-lookup/) and verify the location of your public IP address.

If using NordVPN, be sure to add the environment variable of `NORD_PRIVATE_KEY`.

## Media Stack

This stack contains the following apps:
- Plex
	- Media Management and playback
- Flaresolverr
	- Handles cloudflare for Prowlarr
- Prowlarr
	- Handles and manages the indexers used for searching torrents
- QBittorrent
	- Torrent client
- Radarr
	- Searches and tracks torrents for movies
- Sonarr
	- Searches and tracks torrents for TV shows
- Tdarr
	- Optimizes media library
	- Currently WIP

### Plex

This is the main media server where you will configure your libraries for TV and movies. I also have a USB to OTA adapter, which I use for watching live TV, connected to an OTA antenna.

*Devices*

- `- /dev/dvb:/dev/dvb`
	- This gives the container access to the OTA antenna. 
	- This may change based on what device you're using. If you are not using an antenna, remove this line.
- `- /dev/dri:/dev/dri`
	- This gives the container access to the GPU for hardware acceleration when transcoding files.
	- Depending on your host machine, you may need to modify this line, but without access for transcoding, Plex will fail to play certain media on certain devices.
	
**`network_mode: host`**
Plex must use the host network and is not routed through the VPN so that the Plex server can be accessed remotely. You may need to open some firewall ports to get this working.

**Resources**
Give Plex a bit of extra power for processing media.

**`group_add`**
These also relate to the Devices section, giving access to the GPU and antenna.

**Volumes**
I like persistent files for my configs so I keep those outside of the container storage. 

`- /media:/media`. This is the root folder containing my media files. It is an external drive, mounted to the /media directory. When you set up your Plex libraries, you will go in this folder and select the sub-folder containing the library.

### Tdarr

This is meant for optimizing media files into consistent formats and removing bloat. I am still figuring out how to properly set this up, because it heavily utilizes the GPU for transcoding, and I'm still playing with this. 

When I get it working, I'll update this.
