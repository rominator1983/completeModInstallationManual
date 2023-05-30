# completeModInstallationManual
This repository describes how to get a complete [MOD](https://github.com/moddevices) environment up and running from scratch on a PC using the latest Ubuntu (developed with lunar lobster 23.04) including pipewire support.

I wrote this down for others who would like to use MOD but cannot find a description on what has to be done and how things should work with each other. Basically this describes the setup of my notebook that I use for my guitar effects for jamming and recording guitar tracks. 

# What is MOD on your Linux PC
MOD is a lv2 host that allows you to connect multiple lv2 plugins (effects) in any order you like to modify your input signal (Guitar or everything else). It is not limited to daisy chaining but lets you connect effects in parallel too. This is all based on open source software.

The MOD that you can buy (for example the MOD dwarf) does all of that in a small comfortable box based on fast ARM processors.

# Description of the components

* MOD-host
    Runs lv2 plugins and connects them via jack as desired
* MOD-UI
    Controls MOD-host with a python webserver. Handles your presets.
* MOD-plugin-builder
    A set of scripts that aim to provide a huge list of LV2 plugins for your MOD

# Base setup of operating system
1. Install Ubuntu
     1. Use "Install third-party software for graphics and Wi-Fi hardware"
     2. Use "Download and install support for additional media formats
     3. After installing when asked for sending system information select not to send in order to not cause unneccessary network trafic.
     4. `sudo apt-get install ubuntustudio-installer -y`
2. Install Ubuntu studio and its components   
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

3. Install needed software
        sudo apt-get install git  -y
    
    As of [the documentation](https://github.com/moddevices/mod-host) install the following:
    
        sudo apt install libreadline-dev liblilv-dev lilv-utils libfftw3-dev libjack-jackd2-dev -y
    
    As of [the documentation](https://github.com/moddevices/mod-ui) install the following:
    
        sudo apt-get install virtualenv python3-pip python3-dev git build-essential libasound2-dev libjack-jackd2-dev liblilv-dev libjpeg-dev zlib1g-dev -y
    As of [the documentation](https://github.com/moddevices/mod-plugin-builder) install the following:
    
        sudo apt install acl bc curl cvs git mercurial rsync subversion wget bison bzip2 flex gawk gperf gzip help2man nano perl patch tar texinfo unzip automake binutils build-essential cpio libtool libncurses-dev pkg-config python-is-python3 libtool-bin -y

4. Clone all needed repositories

    Open a console and paste this the following lines

        mkdir mod
        cd mod
        git clone --recurse-submodules https://github.com/moddevices/mod-host.git
        git clone --recurse-submodules https://github.com/moddevices/mod-ui.git
        git clone --recurse-submodules https://github.com/moddevices/mod-plugin-builder.git

5. Follow the build steps on the pages in this order:
     1. https://github.com/moddevices/mod-host
    
               cd ~/mod-host
               make
               sudo make install
    
     2. https://github.com/moddevices/mod-ui

               cd ~/mood-ui
               virtualenv modui-env
               source modui-env/bin/activate
               pip3 install -r requirements.txt
               make -C utils

     3. https://github.com/moddevices/mod-plugin-builder (This will take the longest)

               cd ~/mod-plugin-builder
               # This will take hours on ANY machine
               ./bootstrap.sh x86_64

TODO: Playing audio from browser etc. even when MOD is running
