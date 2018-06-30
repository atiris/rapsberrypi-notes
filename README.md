# Rapsberry Pi 3B notes

The basic installation of the kodi media center on RPI3.  
My notes what to install on clean raspberry pi to setup media center Kodi.

## Install from ISO

Source: https://osmc.tv/download/

**Linux:**

    gzip -dc ~/downloads/OSMC_TGT_rbp2_20180502.img.gz | pv | sudo dd of=/dev/mmcblk0 && sync

**Windows:** https://etcher.io/ (Etcher)

## Set OSMC password (default is 'osmc')

    passwd
    
## Set root password

    sudo passwd
    
## Setup Transmission

Enable Transmission in the menu: “My OSMC -> App Store”  
Web url: http://<device_hostname>:9091/transmission/web/  
Configuration: Through the web interface, the icon in left bottom corner shows the settings

## TV Headend

Enable in menu:  “My OSMC -> App Store”  
Web url: http://<device_hostname>:9981/extjs.html  
First login: osmc osmc

**Install addon:**  
Addons -> My addons -> PVR clients -> Tvheadend... -> Activate  
Then open web interface and start the wizard.

## Samba

Enable in menu:  “My OSMC -> App Store”

Then you can do this manually or with next script.

### Manual samba settings

Create copy of smb.conf -> smb-local.conf  
... and disable in [osmc] section: `browsable = no`  
Create file: /etc/samba/smb-shares.conf

### Automatic scripting setup for samba with this commands

    # Updates
    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes dist-upgrade
    sudo apt-get --assume-yes install mc
    
    # New directories Downloads/Torrents + Files + Movies + Recordings
    sudo mkdir /home/osmc/Downloads /home/osmc/Downloads/Torrents /home/osmc/Files /home/osmc/Movies /home/osmc/Recordings /home/osmc/Recordings/Tv
    sudo chown osmc:users /home/osmc/Downloads /home/osmc/Downloads/Torrents /home/osmc/Files /home/osmc/Movies /home/osmc/Recordings /home/osmc/Recordings/Tv
    sudo chmod 775  /home/osmc/Downloads /home/osmc/Downloads/Torrents /home/osmc/Files /home/osmc/Movies /home/osmc/Recordings /home/osmc/Recordings/Tv
    
    # Disable browsable
    sudo cp /etc/samba/smb.conf /etc/samba/smb-local.conf
    sudo sed -i -E 's/browse?able = yes/browsable = no/g' /etc/samba/smb-local.conf
    
    # Create configuration
    sudo touch /etc/samba/smb-shares.conf 
    echo "[root]
        path = /
        browsable = no
        read only = no
        public = yes
        only guest = yes
        guest only = yes
        writable = yes
        force user = root
        comment = Root access to raspberry

    [Downloads]
        path = /home/osmc/Downloads
        browsable = yes
        read only = no
        public = yes
        only guest = yes
        guest only = yes
        writable = yes
        force user = osmc
        comment = Download

    [Files]
        path = /home/osmc/Files
        browsable = yes
        read only = no
        public = yes
        only guest = yes
        guest only = yes
        writable = yes
        force user = osmc
        comment = Any files except movies

    [Movies]
        path = /home/osmc/Movies
        browsable = yes
        read only = no
        public = yes
        only guest = yes
        guest only = yes
        writable = yes
        force user = osmc
        comment = Movies

    [Recordings]
        path = /home/osmc/Recordings
        browsable = yes
        read only = no
        public = yes
        only guest = yes
        guest only = yes
        writable = yes
        force user = osmc
        comment = Recordings

    [Drives]
        path = /media
        browsable = yes
        read only = no
        public = yes
        only guest = yes
        guest only = yes
        writable = yes
        force user = osmc
        comment = Connected external drives
    " | sudo tee /etc/samba/smb-shares.conf

### Samba restart

    sudo service samba restart

## JDownloader

Wizard to install the current version of [JDownloader](<http://jdownloader.org>) on raspberry pi running osmc kodi. Probably any Debian based linux is supported.  
**Gist:** https://gist.github.com/atiris/34dc670264274b3a472f2a718e4de83a

