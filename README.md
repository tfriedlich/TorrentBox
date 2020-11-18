# TorrentBox
These are my notes for creating my rPi based torrent box. This should include:
* Radarr
* Sonarr
* Deluge

1. Create new user
   - sudo adduser [todd]
   - sudo adduser [todd] sudo
  
2. Update and Upgrade
   - sudo apt-get update
   - sudo apt-get upgrade

3. Install Radarr
   - Download Radarr
      - curl -L -O $( curl -s https://api.github.com/repos/Radarr/Radarr/releases | grep linux.tar.gz | grep browser_download_url | head -1 | cut -d \" -f 4 )
   - Extract
      - tar -xvzf Radarr.develop.*.linux.tar.gz
   - Move
      - sudo mv Radarr /opt

3. Install Sonarr
   - Update Mono
     - sudo apt update && sudo apt install curl mediainfo
   - Add Sonarr's repository to your software source
      - sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 0xA236C58F409091A18ACA53CBEBFF6B99D9B78493
      - echo "deb http://apt.sonarr.tv/ master main" | sudo tee /etc/apt/sources.list.d/sonarr.list
   - Install Sonarr
      ```bash
         sudo apt update
         sudo apt upgrade
         sudo apt install nzbdrone 
      ```
   - [Configure AutoStart] (https://github.com/Sonarr/Sonarr/wiki/Autostart-on-Linux)
      - Create Service File        
        ```bash
           sudo nano /etc/systemd/system/sonarr.service
        ```

**Add and edit this example `sonarr.service` unit**

_Be *sure* to modify the user, group, path to the `mono` binary and install directory._

```
[Unit]
Description=Sonarr Daemon
After=network.target

[Service]
# Change and/or create the required user and group.
User=sonarr
Group=sonarr

# The UMask parameter controls the permissions of folders and files created.
#UMask=002

# The -data=/path argument can be used to force the config/db folder
ExecStart=/usr/bin/mono --debug /opt/NzbDrone/NzbDrone.exe -nobrowser

Type=simple
TimeoutStopSec=20
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
  
