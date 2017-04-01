# OpenVPN Notes

## Client Setup on GNU/Linux

Get the `*.ovpn` configuration file from your VPN service provider. Note the location of this file.

Install the `openvpn` application.

	$ sudo apt install openvpn

Connect to your VPN provider:

	$ sudo openvpn --config /path/to/ovpn/file
