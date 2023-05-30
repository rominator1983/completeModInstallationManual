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

4. Install Ubuntu studio and its components
    
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

5. Install needed software

        sudo apt-get install git  -y
    
    As of [the documentation](https://github.com/moddevices/mod-host) install the following:
    
        sudo apt install libreadline-dev liblilv-dev lilv-utils libfftw3-dev libjack-jackd2-dev -y
    
    As of [the documentation](https://github.com/moddevices/mod-ui) install the following:
        
        sudo apt-get install virtualenv python3-pip python3-dev git build-essential libasound2-dev libjack-jackd2-dev liblilv-dev libjpeg-dev zlib1g-dev -y

    As of [the documentation](https://github.com/moddevices/mod-plugin-builder) install the following:
    
        sudo apt install acl bc curl cvs git mercurial rsync subversion wget bison bzip2 flex gawk gperf gzip help2man nano perl patch tar texinfo unzip automake binutils build-essential cpio libtool libncurses-dev pkg-config python-is-python3 libtool-bin -y

# Clone all needed repositories

Open a console and paste this the following lines

    mkdir mod
    cd mod
    git clone --recurse-submodules https://github.com/moddevices/mod-host.git
    git clone --recurse-submodules https://github.com/moddevices/mod-ui.git
    git clone --recurse-submodules https://github.com/moddevices/mod-plugin-builder.git

Follow the build steps on the pages in this order:
1. https://github.com/moddevices/mod-host
    Use `sudo make install` instead of only `make install`
3. 

TODO: Playing audio from browser etc. even when MOD is running
