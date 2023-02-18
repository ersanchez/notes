# NixOS Notes

Configuration file location:
/etc/nixos/configuration.nix

Rebuilding upon updating configuration file:

1. `$ sudo nixos-rebuild build` - rebuild and nothing more
1. `$ sudo nixos-rebuild switch` - rebuilds, makes it the default boot, restarts services
1. `$ sudo nixos-rebuild test` - rebuilds, switches running system, does not make it default boot
1. `$ sudo nixos-rebuild boot` - rebuild, make it default, does NOT switch now (need to reboot)
1. `$ sudo nixos-rebuild switch -p test` - rebuild, adds submenu to GRUB
