# completeModInstallationManual

This repository describes how to get a complete [MOD](https://github.com/moddevices) environment up and running from scratch on a PC using the latest Ubuntu (developed with lunar lobster 23.04) including pipewire support.

I wrote this down for others who would like to use MOD but cannot find a description on what has to be done and how things should work with each other. Basically this describes the setup of my notebook that I use for my guitar effects for jamming and recording guitar tracks. 

Special thanks to @falkTX for making this all even remotely possible.

# Disclaimer

This is work in progress. Do not use until this notice has been removed.

As this manual is published under the MIT license I cannot be held liable for any damage on any of your hardware or body parts (especially your ears) that result from using this manual. Start things only when setting the volume to 0%.

THIS IS NOT A JOKE. When running MOD carelessly with a laptop sound card with a built in microphone and speakers there can be really unpleasant feedback noises.

# What is MOD on your Linux PC
MOD is an lv2 host that allows you to connect multiple lv2 plugins (aka sound effects) in any order you like to modify your input signal (Guitar, vocals, saxophone or everything else). It is not limited to daisy chaining but lets you connect effects in parallel too. This is all based on open source software and can be used by anyone for free.

The MOD devices that you can buy (for example the MOD dwarf) do all of that in a small form factor and pretty box based on fast ARM processors and decent audio I/O equipment.

However it is possible to use the same software on your PC. You can do this for every day use or just to test things before buying a MOD.
It is however not possible to use the built in MOD store to buy additional plugins/effects provided by the MDO community and the MOD team.

# Description of the components

* MOD-host
    Runs lv2 plugins and connects them via a jack server (not neccessarily jackd) as desired
* MOD-UI
    Controls MOD-host with a python webserver. Handles your presets.
* MOD-plugin-builder
    A suite to build a huge list of LV2 plugins for MOD

# Base setup of operating system
1. Install Ubuntu
     1. Use "Install third-party software for graphics and Wi-Fi hardware"
     2. Use "Download and install support for additional media formats
     3. After installing when asked for sending system information select not to send in order to not cause unneccessary network trafic.
     4. Consider setting your screen timeout to never in order to not have your screen go blank mid session.
     5. `sudo apt-get install ubuntustudio-installer -y`
2. Install Ubuntu studio and its components
    As of [the documentation](https://ubuntustudio.org/ubuntu-studio-installer/) Ubuntu studio components can be installed by starting the launcher (I suppose windows key) and the ubuntu studio installer from there but that did not work for me as the ubuntustudio-installer was not installed out of the box. Instead do the following:
        `sudo apt-get install ubuntustudio-installer -y`
    
    Then start the Ubuntu studio installer by pressing the windows key and searching for "studio" and install the following components
    * linux-lowlatency
    * ubuntustudio-lowlatency-settings
    * ubuntustudio-performance-tweaks
    * ubuntustudio-pipewire-config
    * ubuntustudio-audio
    * ubuntustudio-menu
    
    This will take some minutes. After that restart the machine.

3. Install needed software
        sudo apt-get install git -y
    
    As of [here](https://github.com/moddevices/mod-host), [here](https://github.com/moddevices/mod-ui) and [here](https://github.com/moddevices/mod-plugin-builder) install the following:
    
        sudo apt install git libreadline-dev liblilv-dev lilv-utils libfftw3-dev libjack-jackd2-dev virtualenv python3-pip python3-dev git build-essential libasound2-dev libjack-jackd2-dev liblilv-dev libjpeg-dev zlib1g-dev acl bc curl cvs git mercurial rsync subversion wget bison bzip2 flex gawk gperf gzip help2man nano perl patch tar texinfo unzip automake binutils build-essential cpio libtool libncurses-dev pkg-config python-is-python3 libtool-bin -y
    
4. Clone all needed repositories

    Open a console and paste this the following lines to checkout all the needed source code

        mkdir mod
        cd mod
        git clone --recurse-submodules https://github.com/moddevices/mod-host.git
        git clone --recurse-submodules https://github.com/moddevices/mod-ui.git
        git clone --recurse-submodules https://github.com/moddevices/mod-plugin-builder.git
        git clone https://github.com/rominator1983/completeModInstallationManual.git

5. Build all components. I have linked the things to get you to the detailed build instructions.
     1. https://github.com/moddevices/mod-host
    
               cd ~/mod/mod-host
               make
               sudo make install
    
     2. https://github.com/moddevices/mod-ui

               cd ~/mod/mod-ui
               
               # this is needed to run mod-ui later. This is probably a ubuntu thing
               sudo apt-get remove pipenv -y
               pip install pipenv
               
               virtualenv modui-env
               source modui-env/bin/activate
               pip3 install -r requirements.txt
               # this is needed to run mod-ui later. This is probably a ubuntu thing
               pip install pycryptodomex
               
               # this is stated in requirements.txt as needed for python 3.10 (and later)
               sed -i -e 's/collections.MutableMapping/collections.abc.MutableMapping/' modui-env/lib/python3.11/site-packages/tornado/httputil.py
               
               make -C utils

     3. https://github.com/moddevices/mod-plugin-builder (This will take the longest)

               cd ~/mod/mod-plugin-builder
               # This will take hours on ANY machine
               ./bootstrap.sh x86_64

6. Build all plugins from mod-plugin-builder

    The [documentation](https://github.com/moddevices/mod-plugin-builder) tells you how to build individual plugin packages.
    This is hard and not so much fun. [mod-live-usb](https://github.com/moddevices/mod-live-usb) could be used to build everything at once but aims at a different solution by using a USB stick to run MOD from.
    So I made up this little script to allow you to build all plugin packages at once.
    After building this there are build log files in `~/mod/mod-plugin-builder` where you can check if everything went well for each package.

        # Needed forr SSH connection to github.com which is done by some of the plugin builds
        ssh-keyscan github.com >> ~/.ssh/known_hosts
        cd ~/mod/mod-plugin-builder
        cp ~/mod/completeModInstallationManual/preparePluginCompilation .
        chmod 777 preparePluginCompilation
        ./preparePluginCompilation
        # Again this will take quite a long time to finish
        ./compileAllPlugins

TODO: Copy LV2 plugins without pretty interface?!?
TODO: Describe start scripts and maybe jack setup? Sample rate etc.
