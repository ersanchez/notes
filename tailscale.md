# Tailscale Notes

## Security

By default, when you use the default settings and install and authorize Tailscale, the default user can SSH **as any user** on the target computer!

Example:

* Computer-A: Alice installs Tailscale as root on comptuer A
* Computer-B: Alice installs Tailscale as root on computer B with default settings
* Computer-B: has users Bob, Carol, David
* Computer-A: Alice can SSH into comptuer B as any of the users
  * `ssh bob@computer-b` success!
  * `ssh carol@computer-b` success!
  * `ssh david@computer-b` success!
