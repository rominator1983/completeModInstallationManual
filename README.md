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
     3. After installing when asked for sending system information select not to send this, in order to not cause unneccessary network trafic when you are recording in the studio with MOD.
     4. Consider setting your screen timeout to never in order to not have your screen go blank when jamming with your friends with MOD.
     5. Take some time to think about software updates and when to apply them, since - again - you don't want those to mess with you when you are live on stage with MOD.
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
    
    This will take some minutes. After that better restart the machine and do not just logout/login as the installer tells you.

# Install MOD
1. Install needed software
        sudo apt-get install git -y
    
    As of [here](https://github.com/moddevices/mod-host), [here](https://github.com/moddevices/mod-ui) and [here](https://github.com/moddevices/mod-plugin-builder) install the following:
    
        sudo apt install git libreadline-dev liblilv-dev lilv-utils libfftw3-dev libjack-jackd2-dev virtualenv python3-pip python3-dev git build-essential libasound2-dev libjack-jackd2-dev liblilv-dev libjpeg-dev zlib1g-dev acl bc curl cvs git mercurial rsync subversion wget bison bzip2 flex gawk gperf gzip help2man nano perl patch tar texinfo unzip automake binutils build-essential cpio libtool libncurses-dev pkg-config python-is-python3 libtool-bin -y
    
2. Clone all needed repositories

    Open a console and paste this the following lines to checkout all the needed source code

        cd ~
        mkdir mod
        cd mod
        git clone --recurse-submodules https://github.com/moddevices/mod-host.git
        git clone --recurse-submodules https://github.com/moddevices/mod-ui.git
        git clone --recurse-submodules https://github.com/moddevices/mod-plugin-builder.git
        git clone https://github.com/rominator1983/completeModInstallationManual.git

3. Build all components. I have linked the things to get you to the detailed build instructions.
     1. https://github.com/moddevices/mod-host
    
               cd ~/mod/mod-host
               make
               sudo make install
    
     2. https://github.com/moddevices/mod-ui

               cd ~/mod/mod-ui
               
               # this is needed to run mod-ui later. This is probably a ubuntu thing
               sudo apt-get remove pipenv -y
               
               virtualenv modui-env
               source modui-env/bin/activate
               pip install pipenv
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

4. Build all plugins from mod-plugin-builder

    The [documentation](https://github.com/moddevices/mod-plugin-builder) tells you how to build individual plugin packages.
    This is hard and not so much fun. [mod-live-usb](https://github.com/moddevices/mod-live-usb) could be used to build everything at once but aims at a different solution by using a USB stick to run MOD from.
    So I made up this little script to allow you to build all plugin packages at once.
    After building this there are build log files in `~/mod/mod-plugin-builder` where you can check if everything went well for each package.

        # Needed forr SSH connection to github.com which is done by some of the plugin builds
        ssh-keyscan github.com >> ~/.ssh/known_hosts
        cd ~/mod/mod-plugin-builder
        chmod 777 ~/mod/completeModInstallationManual/preparePluginCompilation
        chmod 777 ~/mod/completeModInstallationManual/runMod
        chmod 777 ~/mod/completeModInstallationManual/killMod
        ~/mod/completeModInstallationManual/preparePluginCompilation
        # Again this will take quite a long time to finish
        ./compileAllPlugins
    TODO: Copy all LV2 plugins without pretty interface too?!?

5. Start Mod for the first time
    1. If you have multiple audio device be sure to select the correct audio device via the Ubuntu settings
    2. Be sure to turn the volume on your main sound device to 0% in order to avoid feedback.
    3. Run MOD
            ~/mod/completeModInstallationManual/runMod
        You should now see a firefox opening with the main MOD window. There are several tutorials on what to do from here so I won't cover those.
    4. Close MOD
        To close MOD again just do
            ~/mod/completeModInstallationManual/killMod

6. Get rid of a dull sound
    When playing around with MOD for the first time with your guitar you might get a sense of a dull sound when just using distortion efffects and reverb and stuff.
    This might be because you are missing out on amp and cabinet simulation. This would also happen if you connected your headphones directoly to a distortion effect pedal. (DO NOT EVEN TRY THIS. IT MIGHT BREAK YOUR EQUIPMENT OR EARS.)
    In order to let MOD shine you need a decent amp simulation in your effect chain, followed by a cabinet simulation. I prefer to use impulse responses for cabinet simulations.
    TODO: Describe how to use impulse responses.
    TODO: Make a simple effect chain setup and describe/copy that here.

7. Pipewire config
    Use the following to check your current sample rate and buffer settings `pw-metadata -m -n settings`
    You can edit `/usr/share/pipewire/pipewire.conf` and uncomment default.clock.allowed-rates and change the value to `[ 44100, 48000, 96000]` and `default.clock.rate` to 96000
    

8. Cnsiderations for starting MOD
    Up to this point you have been setting up MOD to run using pipewires jack server implementation (that you have chosen to install as part of the Ubuntu studio components).
    This means that MOD is now using your systems default audio device with pipewire.
    When you are using a notebook like me you are probably using an external USB sound card and you might want to use that per default with MOD without thinking about it or manually changing devices in the Ubuntu settings.
    To choose the right sound card every time when starting MOD you can change the sound card selection in the runMod script.
    TODO: Add a line with wpctl for my setup! Check how sample rate and bit depth work in pipewire/jack. Check the output of pw-top when running MOD. Checkot https://www.ypsidanger.com/headphone-speaker-fast-switching-with-pipewire/
    Edit `~/mod/completeModInstallationManual/runMod` and uncomment and edit the line with `wpctl ...`

7. Default.json 
    runMod copies a simple/working effect setup (json) to `~/mod/mod-ui/data/last.json`. The reason for that is, that if something breaks an installed LV2 plugin that is in use in your last loaded effect chain, then MOD wont start and you have a hard time figuring out, what's wrong and how to fix it. Thus I have made a simple tuner config that is referenced in a json file that gets copied over before starting MOD. To leverage that, create your own simple default effect chain and copy TODO: finish sentence.

8. Not using pipewire
    If you are old school and do not want to use pipewire you can start MOD with jackd using alsa instead. Part of that is commented out in the runMod script. I have done this with pulseadio prior to lunar lobster but I have not done that with pipewure. Pulseaudio has been able to be be set up to work on base of jack which I have done to be able to play on my guitar and listen to other sutff (youtube etc.) in parallel to play along to. This can be done with pipewire too but since pipewire implements its own jack server I did not go any further and have now abandonded jackd all together. 
    
    
    TODO: Describe start scripts and maybe jack setup? Sample rate etc.
