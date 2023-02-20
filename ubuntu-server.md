# Notes on Ubuntu Server

These notes are current as of Ubuntu 22.04 LTS.

## Hostname

Change hostname:

    $ sudo hostnamectl set-hostname newHostname
  
Note: be sure to update `/etc/hosts` with the new hostname

## Users

Here is how to create users with a specific user id (uid) and a specific group id (gid).

1. create a group for the user

        $ sudo groupadd -g GID groupName

1. create the user

        $ sudo useradd -u UID -g GID -m -s /bin/bash userName
        
1. set the new user's password

        $ sudo passwd <newusername>
        
1. if you want that user to be in the sudo group, add 'em!

        $ sudo adduser <newusername> sudo       
    

## Networking

Configure ip address of the server using Netplan. Configuration flies are in `/etc/netplan`. Look for a \*.yaml file.

Two options for setting the ip address:

1. static ip  
1. static lease (DHCP reservation)

### Static ip

This is how the yaml file will look if you set up the server to connect via one of the ethernet ports from the installer:

    # This is the network config written by 'subiquity'
    network:
      ethernets:
        enp0s25:
          dhcp4: true
      version: 2
          
Replace the line `dhcp4: true` with the desired ip address. For example, if you want to have the ip address 192.168.1.99, you would add this line in the place of the `dhcp4: true` line along with the appropriate subnet mask:

    # This is the network config written by 'subiquity'
    network:
      ethernets:
        enp0s25:
          addresses: [192.168.1.250/24]
          routes:
           - to: default
             via: 192.168.1.1
          nameservers:
            addresses: [192.168.1.1]
      version: 2
      
Apply your changes:

    $ sudo netplan apply
          
### Static Lease

<< coming soon >>
          
### DNS

Sytemd used `systemd-resolved` for DNS. Type `resolvectl` to see your DNS servers:

    $ resolvectl

## Virtualization

This uses KVM and QEMU.

The default directory for the qcow images in KVM is `/var/lib/libvirt/images`.

Install all of the dependencies:

    $ sudo apt install bridge-utils\
    libvirt-clients\
    libvirt-daemon-system\
    qemu-system-x86
    
This will install a few hundred megabytes of dependencies. 

Stop `libvirtd`:

    $ sudo systemctl stop libvirtd

Verify that these groups exist: `kvm` & `libvirt`. If they do not exist, create them.

    $ sudo groupadd kvm
    $ sudo groupadd libvirt

Also verify that your user is in these two groups. If not, add your user to these two groups. Also, add any other users who will be creating or accessing virtual machines on this server.

    $ sudo usermod -aG kvm USERNAME
    $ sudo usermod -aG libvirt USERNAME
    
Set the `kvm` group permissions to access the default vm directory (see directory location above):

    $ sudo chown :kvm /var/lib/libvirt/images
    
Set permissions on the vm directory so that `kvm` group members can read & write to it:

    $ sudo chmod g+rw /var/lib/libvirt/images
    
Restart libvirtd and verify it is running:

    $ sudo systemctl start libvirtd
    $ sudo systemctl status libvirtd
   
