# pixraper
Pixraper, the Pic scraper: Auto-import photos and videos from inserted SD cards
and other media

Pixraper is a WorksForMe[tm] set of scripts and udev configuration to copy or
move all image and video files from an SD card to a configured destination as
soon at the card is inserted.

I use pixraper to move files from my DSLR camera's SD card to my server's
directory structure.

## How's it work?

* udev is triggered when an SD card (or similar medium) is inserted
* A custom (included) udev rule starts the `pixraper.glue`
* `pixraper.glue` mounts the device, checks whether it should be processed by
  pixraper, and then calls
* `pixraper` itself, which copies or moves all matching files[tm] to the
  configured destination, creating subdirectories for each day

## Sounds great. 

Thanks.

## Installation

### 0) Prerequisites

pixraper is written in **perl**, so you will need that. I currently use 5.20.1,
but I guess anything newer than 5.8.8 should work.

The following non-standard perl modules are required:
* **Config::IniFiles**
* All the rest should be in your perl base package

You need a working **`mailx`** on your system to properly send and receive
pixraper mails.

### 1) Install files

Copy (or symlink or whatever) `pixraper.glue` to `/usr/local/sbin` and
`pixraper` to `/usr/local/bin`. Feel free to use different paths, but then you
might have to modify the udev rules or even the scripts themselves.

Copy and rename `udev.99-pixraper.rules` to your system's udev rule directory
(that is `/etc/udev/rules.d/99-pixraper.rules` for me).

Copy and rename systemd.pixraper@.service to one of your system's systemd 
unit file directories (that is `/etc/systemd/system/pixraper@.service` for
me).

Copy `pixraper.conf` to the /etc directory.

### 2) Connect udev and pixraper.glue

The udev ruleset you just installed should usually just work[tm]. If your blkid
works differently than mine, or you used a different path for the script files,
you might have to edit the udev ruleset.

udev needs to be reloaded or restarted to re-read the pixraper rule.
On my OpenSUSE system, I run

	systemctl restart systemd-udevd.service

to do that. Restarting the system will work as well :)

The same goes for the systemd unit file (pixraper@.service): Should work out
of the box, but if your installation directory was somewhere else, go and
edit that file.

systemd will automatically load that file when it is required, so no service
reload or whatever is required for that.

### 3) Configure pixraper

Edit /etc/pixraper.conf:
* The `[pixraper]` section describes properties of pixraper itself:
  * `readAsUser` is the uid pixraper setuid's to: Files need to be readable by
     that user; local directories are owned by that user
   * `PictureDestinationDir` and `MoviesDestinationDir` point to the directories
     where image and video data are put into. If these directories do not
     pre-exist, pixraper will try to create them.
   * `operationMode` can be one of the modes `copy` or `move`.
* In the `[pixraper.glue]` section, please configure
  * `sender` and `recipient` for the mails sent by the system. Nah, I don't want
    to get your pixraper results.
  * `uid` and `gid` are used in the mount options to make the mounted device
    readable by pixraper. This will frequently be identical to the `readAsUser`
    as set in the `[pixraper]` section

### 4) Prepare media

Every memory card to be processed by pixraper needs to contain a file named
`PIXRAPER`. Use `touch PIXRAPER` to create it.

### 5) File suffixes

At this time, pixraper only detects a very limited set of suffixes. Add your
own in pixraper itself. Search for "NEF" (a Nikon RAW format) for the image
format suffixes, and add your own suffixes there; search for MP4 for the
video suffix list.

A configurable list of suffixes should be a future feature.

## Cool. And now?

You should be done now. Try to insert an SD card from your camera.

Works for you? Cool. Feel free to drop me a note on github.

Does not work for you? You are missing features? Well, tell me so via github.

Thanks.

## Disclaimer, License, and stuff

pixraper is licensed under the GPLv3. That means: no charges, Open Source,
redistribute under the same license, and so on. See the included
`LICENSE` file for more information.

Copyright (C) 2016 Bastian Friedrich / bastian@bastian-friedrich.de
