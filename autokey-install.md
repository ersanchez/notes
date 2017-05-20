This is a _draft_ of the installation instructions for AutoKey.

**DO NOT USE IT - It is a work in progress**

# Contents 

[Installation Options](#installation-options)
* [Removing Existing AutoKey](#removing-existing-autokey)
* [Package Manager](#package-manager)

  -[Debian and Derivatives](#debian-and-derivatives)    
  -[Arch](#arch)    
  -[Gentoo](#gentoo)
  
* [pip Install](#pip-install)

[Running AutoKey](#running-autokey)

## Installation Options

There are two versions of the Python3 based AutoKey

* `autokey-gtk` - for systems using a GTK-based desktop environment such as GNOME, MATE, Ubuntu Unity, etc.
* `autokey-qt` - for systems using a Qt-based desktop environment such as KDE Plasma, Lumina, etc.

AutoKey can be installed using

* package manager
* [`pip`][pip]

**NOTE:** Remove existing AutoKey installation before attempting these steps.

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

This assumes you have Python version 3.5 or higher installed.

If you are using a distribution other than those listed above or if you want to install AutoKey from GitHub you can use this [`pip`][pip] installation method.

If `git` is not already installed, [install git][installgit].

If the Python3 version of `pip` is not already installed, install `pip` using these [instructions][installpip].

The following dependencies _should_ be automatically installed by the `pip` install script listed below. If the install fails, look at your error messages - you might need to manually install one or more of the dependencies.

Dependencies:

* `autokey-gtk`
  - GObject Introspection
  - PyGTK
  - GtkSourceView
  - libappindicator
* `autokey-qt`
  - PyQt4
  - PyKDE4
* Both versions
  - dbus-python 
  - pyinotify
  - python-xlib

Install AutoKey from the [AutoKey GitHub repository][autorepo]:

    $ pip3 install --user git+https://github.com/autokey-py3/autokey
  
## Running AutoKey

[aur]: https://aur.archlinux.org/packages/autokey-py3
[autorepo]: https://github.com/autokey-py3/autokey
[installgit]: https://git-scm.com/download/linux
[installpip]: https://pip.pypa.io/en/stable/installing/
[layman]: https://github.com/y2kbadbug/gentoo-overlay/tree/master/app-misc/autokey-py3
[pip]: https://en.wikipedia.org/wiki/Pip_(package_manager)
[ppa]: https://askubuntu.com/a/4990
