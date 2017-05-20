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
* using [`pip`][pip]

### Removing Existing AutoKey

1. Quit Autokey - verify the AutoKey icon is not present in your system tray
1. Backup your `~/.config/autokey` directory if you wish to save existing scripts and phrases
1. Uninstall the existing AutoKey

## Package Manager

### Debian and Derivatives

This section applies to Debian and derivatives such as Ubuntu and Mint. These steps assume that you have Python version 3.5 or higher.

If you don't know what a PPA is you can learn about them [here][ppa].

  $ sudo add-apt-repository ppa:troxor/autokey
  $ sudo apt update
  $ sudo apt install autokey-gtk
  
### Arch

The GTK front end is available in the Arch User Repository ([AUR][aur]). The qt front end is not available in the AUR.

### Gentoo

Install AutoKey using the [layman][layman] package manager.

  layman -a y2kbadbug
  emerge --sync
  emerge -av autokey-py3

## `pip` Install

Dependencies:

* Python: 3.5
* Python modules (common):
  - dbus-python 
  - pyinotify
  - python-xlib
* GTK frontend only:
* GObject Introspection
  - PyGTK
  - GtkSourceView
  - libappindicator
* QT frontend only:
  - PyQt4
  - PyKDE4



[aur]: https://aur.archlinux.org/packages/autokey-py3
[layman]: https://github.com/y2kbadbug/gentoo-overlay/tree/master/app-misc/autokey-py3
[pip]: https://en.wikipedia.org/wiki/Pip_(package_manager)
[ppa]: https://askubuntu.com/a/4990
