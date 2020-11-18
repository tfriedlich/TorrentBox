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
   - Create Sonarr Account
     ```bash
        sudo adduser --system --no-create-home nzbdrone
        sudo mkdir /home/nzbdrone
        sudo chmod 770 /opt/NzbDrone -R
        sudo chmod 770 /home/nzbdrone/ -R
        sudo chown nzbdrone:nogroup /opt/NzbDrone -R
        sudo chown nzbdrone:nogroup /home/nzbdrone -R
     ```     
   - [Configure AutoStart] (https://github.com/Sonarr/Sonarr/wiki/Autostart-on-Linux)
      - Create Service File        
        ```bash
           sudo nano /etc/systemd/system/sonarr.service
        ```

      **Add this to `sonarr.service` file**

      ```
        [Unit]
        Description=Sonarr Daemon
        After=network.target

        [Service]
        User=nzbdrone
        Group=nogroup

        Type=simple
        ExecStart=/usr/bin/mono /opt/NzbDrone/NzbDrone.exe -nobrowser
        TimeoutStopSec=20
        KillMode=process
        Restart=on-failure

        [Install]
        WantedBy=multi-user.target
      ```
  
