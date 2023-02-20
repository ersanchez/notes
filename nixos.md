# NixOS Notes

Beginners, make things easy for yourself at first by doing these things:

* install NixOS on a virtual machine
* do not enable full disk encryption (...yet!)
* do not set a unique root password (...yet!)
* set to boot directly into your user account
    * we'll fix this later
    * this will save tons of time while learning
* after you have a working config
    * set full disk encryption
    * require user password to log in
    * set unique root password

Configuration file location:
/etc/nixos/configuration.nix

Rebuilding upon updating configuration file:

1. `$ sudo nixos-rebuild build` - rebuild and nothing more
1. `$ sudo nixos-rebuild switch` - rebuilds, makes it the default boot, restarts services
1. `$ sudo nixos-rebuild test` - rebuilds, switches running system, does not make it default boot
1. `$ sudo nixos-rebuild boot` - rebuild, make it default, does NOT switch now (need to reboot)
1. `$ sudo nixos-rebuild switch -p test` - rebuild, adds submenu to GRUB
