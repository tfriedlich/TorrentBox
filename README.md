# TorrentBox
These are my notes for creating my rPi based torrent box. This should include:
* Radarr
* Sonarr
* Deluge

1. Create new user
   ```bash
      sudo adduser [todd]
      sudo adduser [todd] sudo
   ```
2. Update and Upgrade
   ```bash
      sudo apt-get update
      sudo apt-get upgrade
   ```
   
5. Install Deluged (https://www.serverself.com/how-to-setup-deluge-a-torrent-seedbox-server/)
   - Add add-apt Repository
      ```bash 
         sudo apt install software-properties-common
      ```
   - Create Deluge user and group
      ```bash 
         sudo adduser --system --group deluge
         sudo gpasswd -a root deluge
      ```
    - Install Deluged (https://linuxconfig.org/set-up-a-headless-deluge-server-on-linux)
      ```bash
         sudo apt install deluged deluge-console
      ```
    
    - Configure AutoStart 
      - Create Service File        
        ```bash
          nano /etc/systemd/system/deluged.service
        ```

      - Add this to `deluged.service` file

        ```
        [Unit]
        Description=Deluge Bittorrent Client Daemon
        After=network-online.target

        [Service]
        Type=simple
        User=deluge
        Group=deluge
        UMask=007

        ExecStart=/usr/bin/deluged -d

        Restart=on-failure

        # Configures the time to wait before service is stopped forcefully.
        TimeoutStopSec=300

        [Install]
        WantedBy=multi-user.target
        ```
      - Enable Service
       ```bash
          sudo systemctl start deluged
          sudo systemctl enable deluged
       ```
       
      - Set up Web Service
      ```bash 
         sudo nano /etc/systemd/system/deluge-web.service
      ```
      - Add this to `deluge-web.service` file

        ```
        [Unit]
        Description=Deluge Bittorrent Client Web Interface
        After=network-online.target

        [Service]
        Type=simple

        User=deluge
        Group=deluge
        UMask=027

        ExecStart=/usr/bin/deluge-web

        Restart=on-failure

        [Install]
        WantedBy=multi-user.target
        ```      
      - Enable Web Service
      ```bash 
         sudo systemctl start deluge-web
         sudo systemctl enable deluge-web
      ```
      
3. Install Radarr
   - Download Radarr
      ```bash
         curl -L -O $( curl -s https://api.github.com/repos/Radarr/Radarr/releases | grep linux.tar.gz | grep browser_download_url | head -1 | cut -d \" -f 4 )
      ```
   - Extract
     ```bash
        tar -xvzf Radarr.develop.*.linux.tar.gz
     ```
   - Move
     ```bash
        sudo mv Radarr /opt
     ```
   - Create Radarr Account
     ```bash
        sudo adduser --system --no-create-home radarr
        sudo mkdir /home/radarr
        sudo chmod 770 /opt/Radarr -R
        sudo chmod 770 /home/radarr/ -R
        sudo chown nzbdrone:nogroup /opt/Radarr -R
        sudo chown nzbdrone:nogroup /home/radarr -R        
      ```
    - Configure AutoStart (https://github.com/Radarr/Radarr/wiki/Autostart-on-Linux)
      - Create Service File        
        ```bash
           sudo nano /etc/systemd/system/radarr.service
        ```

      - Add this to `radarr.service` file

      ```
      [Unit]
      Description=Radarr Daemon
      After=syslog.target network.target
      
      [Service]      
      User=radarr
      Group=nogroup

      Type=simple

      ExecStart=/usr/bin/mono --debug /opt/Radarr/Radarr.exe -nobrowser
      TimeoutStopSec=20
      KillMode=process
      Restart=on-failure

      #ReadWritePaths=/opt/Radarr /path/to/movies/folder
      #ProtectSystem=strict
      #PrivateDevices=true
      #ProtectHome=true

      [Install]
      WantedBy=multi-user.target      
      ```
      - Enable Service
       ```bash
          sudo systemctl enable radarr
       ```      
3. Install Sonarr
   - Update Mono
     ```bash
        sudo apt update && sudo apt install curl mediainfo
     ```
   - Add Sonarr's repository to your software source
     ```bash
        sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 0xA236C58F409091A18ACA53CBEBFF6B99D9B78493
        echo "deb http://apt.sonarr.tv/ master main" | sudo tee /etc/apt/sources.list.d/sonarr.list
     ```
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
   - Configure AutoStart (https://github.com/Sonarr/Sonarr/wiki/Autostart-on-Linux)
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
     - Enable Service
       ```bash
          sudo systemctl enable sonarr
       ```

    
