This is a _draft_ of the installation instructions for AutoKey.

**DO NOT USE IT - It is a work in progress**

# Contents 

[Installation Options](#installation-options)    
- [Removing Existing AutoKey](#removing-autokey)
- [Package Manager](#package-manager)
- [Git Repository](#git-repository)
- [Build from Source](#build-from-source)

## Installation Options

**NOTE:** Remove existing AutoKey installation before attempting these steps.

AutoKey can be installed by

* using a package manager
* using a Git repository
* building from source

### Removing Existing AutoKey

1. Quit Autokey - verify the AutoKey icon is not present in your system tray
1. Backup your `~/.config/autokey` directory if you wish to save your current scripts and phrases

## Package Manager

### Debian and Derivatives

This applies to Debian and Debian-derived distributions such as Ubuntu and Mint. These steps assume that you have Python version 3.5 or higher.

If you don't know what a PPA is you can learn about them [here][ppa].

  $ sudo add-apt-repository ppa:troxor/autokey
  $ sudo apt update
  $ sudo apt install autokey-gtk
  
### Arch

### Gentoo

## Git Repository

## Build from Source




[ppa]: https://askubuntu.com/a/4990
