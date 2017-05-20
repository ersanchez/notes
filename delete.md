# Contents 
====

[Installation Options](#installation-options)    
[Removing AutoKey](#removing-autokey)


## Installation Options

**NOTE:** Remove prior versions of AutoKey before attempting these steps.

AutoKey can be installed by

* using a Git repository
* using a deb package
* building from source

## Removing AutoKey

1. Quit Autokey - verify the AutoKey icon is not present in your system tray
1. Backup your `~/.config/autokey` directory if you wish to save your current scripts and phrases

## Install From Repository

### Debian and Derivatives

This applies to Debian and Debian-derived distributions such as Ubuntu and Mint. These steps assume that you have Python version 3.5 or higher.

If you don't know what a PPA is you can learn about them [here][ppa].

  $ sudo add-apt-repository ppa:troxor/autokey
  $ sudo apt update
  $ sudo apt install autokey-gtk




[ppa]: https://askubuntu.com/a/4990
