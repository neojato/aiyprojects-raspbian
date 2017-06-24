This repository contains the source code for the AIYProjects "Voice Kit". See
https://aiyprojects.withgoogle.com/voice/.

If you're using Rasbian instead of Google's provided image, read
[HACKING.md](HACKING.md) for information on getting started.

[![Build Status](https://travis-ci.org/google/aiyprojects-raspbian.svg?branch=master)](https://travis-ci.org/google/aiyprojects-raspbian/builds)
[![Test Coverage](https://codecov.io/gh/google/aiyprojects-raspbian/branch/master/graph/badge.svg)](https://codecov.io/gh/google/aiyprojects-raspbian)

## Troubleshooting

The scripts in the `checkpoints` directory verify the Raspberry Pi's setup.
They can be run from the desktop shortcuts or from the terminal.

# Upgrading your AIY Project

Updated May 2017

Log into your Raspberry Pi, or use the Start Dev Terminal from the desktop:

```
# UPDATE GOOGLE ASSISTANT SDK
cd ~/assistant-sdk-python
git checkout master
git pull origin master

# UPDATE VOICE RECOGNIZER (AKA: AIY PROJECTS)
cd ~/voice-recognizer-raspi
git checkout master
git pull origin master

# UPDATE DEPENDENCIES
cd ~/voice-recognizer-raspi 
rm -rf env      # needs to be rm'd w/current version of install-deps.sh
scripts/install-deps.sh
```

As long as you did not modify any of the files, those should have ran smoothly. If you, like me, were tinkering with the `src/main.py` and got a conflict, erase the changes by using `git checkout <file>` (make a backup if you want first with `cp <file> <file>~`).

Test everything is working:

```
sudo systemctl stop voice-recognizer.service    # if you had it running
cd ~/voice-recognizer-raspi
source env/bin/activate
src/main.py
```

## New Config Files

You may want to backup your existing config file and bring over the newest versions, then re-implement your configurations.

```
# back them up first
cp ~/.config/status-led.ini ~/.config/status-led.ini~
cp ~/.config/voice-recognizer.ini ~/.config/voice-recognizer.ini~

# copy the new ones over
cp ~/voice-recognizer-raspi/config/status-led.ini.default ~/.config/status-led.ini
cp ~/voice-recognizer-raspi/config/voice-recognizer.ini.default ~/.config/voice-recognizer.ini

# open and review the new options
nano ~/.config/voice-recognizer.ini
```

## Enabling “OK Google” and “Hey Google” Hot Words

Edit the `voice-recognizer.ini` file as per the original setup docs:

```
nano ~/.config/voice-recognizer.ini
```

Make the trigger say “ok-google”:

```
# Select the trigger: gpio (default), clap, ok-google.
trigger = ok-google
```

NOTE: You can only have one trigger with the current project at this time.

Press `CTRL-X` and `Y` to save. Then restart the service:

```
sudo systemctl restart voice-recognizer.service 
```

Now, speaking “OK Google” or “Hey Google” should work.

## Setting to Auto-Start on Reboot/Power On

```
# UPDATE AUTO-START SERVICES ON BOOT (optional)
cd ~/voice-recognizer-raspi 
sudo scripts/install-services.sh 
sudo systemctl start status-led.service 
sudo systemctl start status-monitor.service
sudo systemctl start voice-recognizer.service 
sudo reboot
```
