# completeModInstallationManual

This repository describes how to get a complete [MOD](https://github.com/moddevices) environment up and running from scratch on a PC using the latest Ubuntu (developed with lunar lobster 23.04) including pipewire.

I wrote this down for others who would like to use MOD but cannot find a description on what has to be done and how things should work with each other. Basically this describes the setup of my notebook that I use for my guitar effects for jamming and recording guitar tracks. 

Special thanks to @falkTX for making this all even remotely possible.

# Disclaimer

As this manual is published under the MIT license I cannot be held liable for any damage on any of your hardware or body parts (especially your ears) that result from using this manual (or not following it). Start MOD only when the volume is set to 0%.
~/mod/mod-plugin-builder
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
     6. In the sound settings set "Alert sounds" to "None".

2. Setup script (this takes very long!)

    sudo apt-get install wget
    wget https://raw.githubusercontent.com/rominator1983/completeModInstallationManual/main/setup -O setupMod
    chmod 777 setupMod
    ./setupMod

3. Install Ubuntu studio and its components    
    Then start the Ubuntu studio installer by pressing the windows key and searching for "studio" and install the following components
    * linux-lowlatency
    * ubuntustudio-lowlatency-settings
    * ubuntustudio-performance-tweaks
    * ubuntustudio-audio
    
    This will take some minutes. After that better restart the machine and do not just logout/login as the installer tells you.

# Install/Build MOD               
1. setup script

This also installs some performance tweaks to the grub bootloader.

        sudo apt-get install wget
        wget https://raw.githubusercontent.com/rominator1983/completeModInstallationManual/main/setup -O setupMod
        chmod 777 setupMod
        ./setupMod
        
2. Edit `~/mod/completeModInstallationManual/runMod` to set the buffer size and numbe of periods of jack. (256 and 3 in the following example)

    jackd -R -P 80 -d alsa -d hw:UR22,0 -r 48000 -p 256 -n 4 -X seq &

# Start Mod for the first time

1. If you have multiple audio device, be sure to select the correct audio device via the Ubuntu settings
2. Be sure to turn the volume on your main sound device to 0% in order to avoid feedback.

    This is neccessary because MOD connects the default input to the default output per default. If you are sitting on a laptop this will result in instant unpleasant and ear damaging feedback! :-( You have been warned!
    `wpctl set-volume @DEFAULT_AUDIO_SINK@ 0%`

4. Run MOD

        ~/mod/completeModInstallationManual/runMod
        
    You should now see firefox opening with the main MOD window. There are several tutorials on what to do from here so I won't cover those in detail.
        
5. Stop MOD

        ~/mod/completeModInstallationManual/killMod

# Polish your sound and user directories
Get rid of a dull sound by using impulse responses and neural networks.

When playing around with MOD for the first time with your guitar you might get a sense of a dull sound when just using distortion efffects and reverb and stuff when you are using MOD with headphones or PA (that is without a guitar/bass amplifier and case or an amplifier combo).
This might be because you are missing out on amp and cabinet simulation which you do need in the effect chain to get a decent sound.
In order to let MOD shine you normally need a decent amp simulation in your effect chain followed by a cabinet simulation jsut as you would set up when using a real amp and cabinet. I prefer to use neural network amp simulations and impulse responses for cabinet simulations.
        
Then get some impulse responses from [valhalir](https://valhallir.at/) or [anywhere on the internet](https://producelikeapro.com/blog/best-guitar-impulse-responses/) and place them `~/mod-workdir/user-files/Speaker Cabinets IRs`.
    
Then get more amp models from [the MOD community](https://forum.mod.audio/t/list-of-shared-models/9631) and place them in `~/mod-workdir/user-files/Aida DSP Models`.
    
Then you can use the plugins 'IR loader cabsim' and 'aida-x' and get the best tones.

Instead of 'aida-x' you can also use the 'Neural Amp Modeler' plugin. Then you can get tones from [tonehunt](https://tonehunt.org/) and place them in `¨/mod-workdir/user-files/NAM\ Models`.
If you experience performance issues follow the documentation [here](https://github.com/mikeoliphant/neural-amp-modeler-lv2):
    
> If you are having trouble running a "standard" model, try looking for "feather" (the least expensive) models. You can find a list of ["feather"-tagged models on ToneHunt](https://tonehunt.org/?tags=feather-mdl). Note that tagging models is up to the submitter, so not all "feather" models are tagged as such - you should be able to find more if you dig around.
    
# Considerations for starting MOD
    
1. Default.json 

    `~/mod/completeModInstallationManual/runMod` copies a simple/working effect setup (json) to `~/mod/mod-ui/data/last.json`. The reason for that is, that if something (an update) breaks an installed LV2 plugin that is in use in your last loaded effect chain, then MOD wont start and you have a hard time figuring out, what's wrong and how to fix it. Thus I have made a simple tuner config that is referenced in a json file that gets copied over before starting MOD. To leverage that, create your own simple default effect chain and copy `~/mod/mod-ui/data/last.json` to `~/mod/mod-ui/data/last.json.default`
