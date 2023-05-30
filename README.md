# completeModInstallationManual
This repository describes how to get a complete MOD installation up and running from scratch on a PC using the latest Ubuntu (developed with lunar lobster 23.04) including pipewire support.

I wrote this down for others who would like to use MOD but cannot find a description on what has to be done and how. Basically this describes the setup of my notebook that I use for guitar effects for jamming and recording guitar tracks. 

# Link collection
TODO

# Base setup of operating system
1. Install Ubuntu
     1. Use "Install third-party software for graphics and Wi-Fi hardware"
     2. Use "Download and install support for additional media formats
     3. After installing when asked for sending system information select not to send in order to not cause unneccessary network trafic.
     4. `sudo apt-get install ubuntustudio-installer -y`

4. Install Ubuntu studio and needed components
    
    `sudo apt-get install ubuntustudio-installer -y`

    As of [the documentation](https://ubuntustudio.org/ubuntu-studio-installer/) Ubuntu studio can be installed by starting the launcher by pressing the windows key and searching for "ubuntu studio" but that did not work for me as the ubuntustudio-installer was not installed out of the box.

    Then start the Ubuntu studio installer by pressing the windows key and searching for "studio" and install the following components
    
    * linux-lowlatency
    * ubuntustudio-lowlatency-settings
    * ubuntustudio-performance-tweaks
    * ubuntustudio-pipewire-config
    * ubuntustudio-audio
    * ubuntustudio-menu
    
    This will take some minutes. After tht restart the machine.

# Clone all needed repositories

TODO

TODO: Playing audio from browser etc. even when MOD is running
