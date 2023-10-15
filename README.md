# completeModInstallationManual

This repository describes how to get a complete [MOD](https://github.com/moddevices) environment up and running from scratch on a PC using the latest Ubuntu (developed with lunar lobster 23.04) including pipewire.

I wrote this down for others who would like to use MOD but cannot find a description on what has to be done and how things should work with each other. Basically this describes the setup of my notebook that I use for my guitar effects for jamming and recording guitar tracks. 

Special thanks to @falkTX for making this all even remotely possible.

# Disclaimer

As this manual is published under the MIT license I cannot be held liable for any damage on any of your hardware or body parts (especially your ears) that result from using this manual (or not following it). Start MOD only when the volume is set to 0%.

THIS IS NOT A JOKE. When running MOD carelessly with a laptop sound card with a built-in microphone and speakers there can be really unpleasant/deafening feedback noises.

# What is MOD on your Linux PC
MOD is an [lv2](https://lv2plug.in/) host that allows you to connect multiple lv2 plugins (aka sound effects) in any order you like to modify your input signal (Guitar, vocals, saxophone or everything else). It is not limited to daisy chaining but lets you connect effects in parallel too (dual amping). This is all based on open source software and can be used by anyone for free.

The [MOD dwarf](https://mod.audio/dwarf/) that you can buy here https://www.thomann.de/intl/mod_dwarf.htm does all of that in a small form factor and pretty box based on fast ARM processors and decent audio I/O equipment.

However it is possible to use the same software on your PC with some work. You can do this for every day use or just to test things before buying a MOD.
This way however it is not possible (as far as I know) to use the MOD store to buy additional plugins/effects provided by the MDO community and the MOD team.

# Description of the used components

* MOD-host

    Runs lv2 plugins and connects them via a jack server (not neccessarily jackd).
    
* MOD-UI

    Controls MOD-host with a python webserver. Handles your presets.
    
* MOD-plugin-builder

    A suite to build a huge list of LV2 plugins for MOD.

* pipewire
    
    The new audio sub system that ships with newer Ubuntu releases since lunar lobster (23.04).
    With pipewire-jack there is an implementation of a jack server (that is, it is not jackd nor does it use or need jackd).

* JACK

    The audio backend API/server that the MOD-host uses to connect lv2 plugins with each other. Pipewire implements the JACK protocol thus eliminating the need for a JACK server.

# Base setup of operating system

1. Install Ubuntu
     1. Use "Install third-party software for graphics and Wi-Fi hardware"
     2. Use "Download and install support for additional media formats
     3. After installing when asked for sending system information select not to send this, in order to not cause unneccessary network trafic at the worst possible time on stage with your MOD.
     4. Consider setting your screen timeout to never in order to not have your screen go blank when jamming with your friends with MOD.
     5. Take some time to think about how and when to apply software updates to your box, since - again - you don't want those to mess with you when you are live on stage with MOD.

2. Install Ubuntu studio and its components

    As of [the documentation](https://ubuntustudio.org/ubuntu-studio-installer/) Ubuntu studio components can be installed by starting the launcher (I suppose windows key) and the ubuntu studio installer from there but that did not work for me as the ubuntustudio-installer was not installed out of the box. Instead do the following:
        
        sudo apt-get install ubuntustudio-installer -y
    
    Then start the Ubuntu studio installer by pressing the windows key and searching for "studio" and install the following components
    * linux-lowlatency
    * ubuntustudio-lowlatency-settings
    * ubuntustudio-performance-tweaks
    * ubuntustudio-audio
    
    This will take some minutes. After that better restart the machine and do not just logout/login as the installer tells you.

3. Use latest pipewire versionwith PPA

    `sudo add-apt-repository ppa:pipewire-debian/pipewire-upstream`
   
    `sudo add-apt-repository ppa:pipewire-debian/wireplumber-upstream`

    `sudoa apt-get update && sudo apt-get upgrade`

5. Performance tweaks

    You can set pre-empting to full to make your box more realtime compatible at the cost of throughput. This is done by editing the file `/etc/default/grub` and editing the line with `GRUB_CMDLINE_LINUX_DEFAULT` to `GRUB_CMDLINE_LINUX_DEFAULT ="quiet splash preempt=full`. If you have performance troubles and are not using the box for anything else other than audio you can turn off security mitigations for intel processors. From a security standpoint this is NOT A GOOD IDEA: `GRUB_CMDLINE_LINUX_DEFAULT ="quiet splash preempt=full mitigations=off`

    After that you do a `sudo update-grub`

6. Deactivate Alerts

   In the sound settings set "Alert sounds" to "None".

# Install/Build MOD

1. Install needed software

        sudo apt-get install git -y
    
    As of [here](https://github.com/moddevices/mod-host), [here](https://github.com/moddevices/mod-ui) and [here](https://github.com/moddevices/mod-plugin-builder) install the following (be sure to copy the complete very very long line):
    
        sudo apt install git libreadline-dev liblilv-dev lilv-utils libfftw3-dev libjack-jackd2-dev virtualenv python3-pip python3-dev git build-essential libasound2-dev libjack-jackd2-dev liblilv-dev libjpeg-dev zlib1g-dev acl bc curl cvs git mercurial rsync subversion wget bison bzip2 flex gawk gperf gzip help2man nano perl patch tar texinfo unzip automake binutils build-essential cpio libtool libncurses-dev pkg-config python-is-python3 libtool-bin libmtdev-dev libsqlclient-dev libsqlitecpp-dev libpulse-dev libx11-dev libfontconfig1-dev libc++-dev glibc-source linux-libc-dev -y
    
2. Clone all needed repositories

    Open a console and paste the following lines to checkout all the needed source code

        cd ~
        mkdir mod
        cd mod
        git clone --recurse-submodules https://github.com/moddevices/mod-host.git
        git clone --recurse-submodules https://github.com/moddevices/mod-ui.git
        git clone --recurse-submodules https://github.com/moddevices/mod-plugin-builder.git
        git clone https://github.com/rominator1983/completeModInstallationManual.git

3. Build all components. I have linked the things to get you to the detailed build instructions if needed.
     1. https://github.com/moddevices/mod-host
    
               cd ~/mod/mod-host
               make
               sudo make install
    
     2. https://github.com/moddevices/mod-ui

               cd ~/mod/mod-ui
               
               # this was sometimes needed to run mod-ui later. This is probably a ubuntu thing
               sudo apt-get remove pipenv -y
               
               virtualenv modui-env
               source modui-env/bin/activate
               pip install pipenv
               pip3 install -r requirements.txt
               
               # this is also needed to run mod-ui later
               pip install pycryptodomex
               
           This is stated in requirements.txt as needed for python 3.10 (and obviously later too).
               
               pyMinorVersion="$(python3 -c 'import sys; print(sys.version_info[:][1])')"
               sed -i -e 's/collections.MutableMapping/collections.abc.MutableMapping/' modui-env/lib/python3."$pyMinorVersion"/site-packages/tornado/httputil.py
           
           Build
               
               make -C utils

     3. https://github.com/moddevices/mod-plugin-builder (This will take the longest)

               cd ~/mod/mod-plugin-builder
               # This will take hours on ANY machine
               ./bootstrap.sh x86_64

4. Build all plugins from mod-plugin-builder

    The [documentation](https://github.com/moddevices/mod-plugin-builder) tells you how to build individual plugin packages.
    This is hard and not so much fun. [mod-live-usb](https://github.com/moddevices/mod-live-usb) could be used to build everything at once but aims at a different solution by using a USB stick to run MOD from.
    So I made up [this little script](https://github.com/rominator1983/completeModInstallationManual/blob/main/preparePluginCompilation) to allow you to build all plugin packages at once.

        # Needed for SSH connection to github.com which is done by some of the plugin builds
        ssh-keyscan github.com >> ~/.ssh/known_hosts
        cd ~/mod/mod-plugin-builder
        chmod 777 ~/mod/completeModInstallationManual/preparePluginCompilation
        ~/mod/completeModInstallationManual/preparePluginCompilation
        
        # Again this will take quite a long time to finish
        ./compileAllPlugins
    
    After that is done you can check the build output of the different plugin packages in `build{package}.log`. In the end do the following to copy the plugins to your computers lv2 directory to enjoy more than 1000 effects at you fingertips:
        
        sudo cp -r ~/mod-workdir/x86_64/plugins/* /usr/lib/lv2/

   Note: This copies not only all lv2 plugins with abeautiful user interface but also a lot of plugins that only have a basic ui that is provided by MOD-UI.

# Pipewire/Jack config
    
The following settings in `/usr/share/pipewire/pipewire.conf` have to be made since 48000 is also the only used frequency for neural networks amp simulations (See further below): 
    
    default.clock.allowed-rates = [ 48000, 96000 ]
    default.clock.rate = 48000
        
MOD-host makes some assumptions on how jack things are named that are not true for the pipewire implementation of jack.
So the following settings in `/usr/share/pipewire/jack.conf` have to be made:
    
    jack.short-name = true
    jack.filter-name = true
    jack.filter-char = " "
    jack.self-connect-mode = allow
    jack.default-as-system = true
    # this solves some issue with crackles stemming from sample rate conversion.
    node.rate = 1/48000

When doing pw-top one node is left that runs at 44/16. Edit `/usr/share/pipewire/client-rt.conf`:

    alsa.rate = [ 48000 ]
    alsa.format = [ S24 ]
        
To check your current sample rate and buffer settings do `pw-metadata -m -n settings`
To check the sample rate, buffer (aka 'quantum' in pipewire) and sample size/format of running applications run `pw-top`

When running MOD all sample rates should be the same (48000) otherwise you will hear some faint but disturbing crackles.

Edit `~/mod/completeModInstallationManual/runMod` to set the sample rate and buffer size (aka 'quantum' in pipewire) too before starting MOD. Start with a higher quantum (512) and reduce until things stop working.

    pw-metadata -n settings 0 clock.force-rate 48000
    pw-metadata -n settings 0 clock.min-quantum 512
    pw-metadata -n settings 0 clock.max-quantum 512
    pw-metadata -n settings 0 clock.force-quantum 512

If you experience issues consult [the documentation](https://gitlab.freedesktop.org/pipewire/pipewire/-/wikis/Config-JACK?version_id=336a4cac3eaa9cdbf20d894e815336da3c34c3d6)

# Crackles on USB device

I use a Steinberg audio interface that is capable of very short latency. Without the proper audio settings this left me with very faint but audible and annoying crackles.

I overcame this by setting the following things in `/usr/share/wireplumber/main.lua.d/50-alsa-config.lua`

    ["priority.driver"] = 1200,
    ["priority.session"] = 1200,
    ["node.pause-on-idle"] = false,

    # Try different (higher) values if this does not work.
    ["api.alsa.headaroom"] = 128
    ["api.alsa.period-size"] = 128
    # This was not needed for me but might be for you
    ["api.alsa.disable-mmap"] = false

# Start Mod for the first time

1. If you have multiple audio device, be sure to select the correct audio device via the Ubuntu settings
2. Be sure to turn the volume on your main sound device to 0% in order to avoid feedback.

    This is neccessary because MOD connects the default input to the default output per default. If you are sitting on a laptop this will result in instant unpleasant and ear damaging feedback! :-( You have been warned!
    `wpctl set-volume @DEFAULT_AUDIO_SINK@ 0%`

4. Run MOD

        chmod 777 ~/mod/completeModInstallationManual/runMod
        ~/mod/completeModInstallationManual/runMod
        
    You should now see firefox opening with the main MOD window. There are several tutorials on what to do from here so I won't cover those in detail.
        
5. Stop MOD

        chmod 777 ~/mod/completeModInstallationManual/killMod
        ~/mod/completeModInstallationManual/killMod

# Polish your sound and user directories
Get rid of a dull sound by using impulse responses and neural networks.

When playing around with MOD for the first time with your guitar you might get a sense of a dull sound when just using distortion efffects and reverb and stuff when you are using MOD with headphones or PA (that is without a guitar/bass amplifier and case or an amplifier combo).
This might be because you are missing out on amp and cabinet simulation which you do need in the effect chain to get a decent sound.
In order to let MOD shine you normally need a decent amp simulation in your effect chain followed by a cabinet simulation jsut as you would set up when using a real amp and cabinet. I prefer to use neural network amp simulations and impulse responses for cabinet simulations.
    
`~/mod/completeModInstallationManual/runMod` sets the variable `MOD_USER_FILES_DIR` to `~/mod-workdir/user-files` (instead of the default `/data/user-files`).
So do this:
    
    mkdir ~/mod-workdir/user-files
    mkdir ~/mod-workdir/user-files/Audio\ Loops
    mkdir ~/mod-workdir/user-files/Audio\ Recordings
    mkdir ~/mod-workdir/user-files/Audio\ Samples
    mkdir ~/mod-workdir/user-files/Audio\ Tracks
    mkdir ~/mod-workdir/user-files/Speaker\ Cabinets\ IRs
    mkdir ~/mod-workdir/user-files/Hydrogen\ Drumkits
    mkdir ~/mod-workdir/user-files/Reverb\ IRs
    mkdir ~/mod-workdir/user-files/MIDI\ Clips
    mkdir ~/mod-workdir/user-files/MIDI\ Songs
    mkdir ~/mod-workdir/user-files/SF2\ Instruments
    mkdir ~/mod-workdir/user-files/SFZ\ Instruments
    mkdir ~/mod-workdir/user-files/Aida\ DSP\ Models
    mkdir ~/mod-workdir/user-files/NAM\ Models
        
    # copy misplaced neural network definitions to user files directory
    cp /usr/lib/lv2/rt-neural-generic.lv2/models/deer\ ink\ studios/* ~/mod-workdir/user-files/Speaker\ Cabinets\ IRs
    
Then get some impulse responses from [valhalir](https://valhallir.at/) or [anywhere on the internet](https://producelikeapro.com/blog/best-guitar-impulse-responses/) and place them `~/mod-workdir/user-files/Speaker Cabinets IRs`.
    
Then get more amp models from [the MOD community](https://forum.mod.audio/t/list-of-shared-models/9631) and place them in `~/mod-workdir/user-files/Aida DSP Models`.
    
Then you can use the plugins 'IR loader cabsim' and 'aida-x' and get the best tones.

Instead of 'aida-x' you can also use the 'Neural Amp Modeler' plugin. Then you can get tones from [tonehunt](https://tonehunt.org/) and place them in `¨/mod-workdir/user-files/NAM\ Models`.
If you experience performance issues follow the documentation [here](https://github.com/mikeoliphant/neural-amp-modeler-lv2):
    
> If you are having trouble running a "standard" model, try looking for "feather" (the least expensive) models. You can find a list of ["feather"-tagged models on ToneHunt](https://tonehunt.org/?tags=feather-mdl). Note that tagging models is up to the submitter, so not all "feather" models are tagged as such - you should be able to find more if you dig around.
    
# Considerations for starting MOD

1. Device selection
    
    Up to this point you have been setting up MOD to run using pipewires jack server implementation.
    This means that MOD is using your systems selected audio device with pipewire. Therefor you can also play a youtube video or do some ardour magic when running MOD.
    When you are using a notebook like me you are probably using an external USB sound card for better latency and sound quality and you might want to use this device per default when running MOD without thinking about it or manually changing devices in the Ubuntu settings. This is especially true considering the ear deafing feedback that might occurr when sitting in front of a laptop that I mentioned several times.
    To choose the right sound card every time when starting MOD you can change the sound card selection in the runMod script.
    
    At the moment it seems there is no convenient pipewire way of doing this as `wpctl` has the problem of changing IDs so the pulseaudio command line tools seem the way to go for whatever reason:

        sudo apt install pulseaudio-utils -y
        
    To find out your desired audio input/output select the correct device in Ubuntu and then do

        pactl get-default-sink
        pactl get-default-source
    
    Copy the respective outputs of those statements and uncomment the corresponding lines in `~/mod/completeModInstallationManual/runMod` to look something like this:

        pactl set-default-sink "alsa_output.usb-Yamaha_Corporation_Steinberg_UR22-00.analog-stereo"
        pactl set-default-source "alsa_input.usb-Yamaha_Corporation_Steinberg_UR22-00.analog-stereo"    
    
2. Default.json 

    `~/mod/completeModInstallationManual/runMod` copies a simple/working effect setup (json) to `~/mod/mod-ui/data/last.json`. The reason for that is, that if something (an update) breaks an installed LV2 plugin that is in use in your last loaded effect chain, then MOD wont start and you have a hard time figuring out, what's wrong and how to fix it. Thus I have made a simple tuner config that is referenced in a json file that gets copied over before starting MOD. To leverage that, create your own simple default effect chain and copy `~/mod/mod-ui/data/last.json` to `~/mod/mod-ui/data/last.json.default`
