# Introduction to Networking

Networking is the act of connecting one or more computers together so that they may communicate with one another. This section will cover some really basic concepts to get you familiar with how to network Linux machines and basic network tools.

## Networks in General

In normal computers, networks are utilised via either and Ethernet/LAN port or a WiFi adapter. You may have a LAN port on your laptop, but some models do not have it. However, most desktop computers have one or more of these ports.

<span style="display:block;text-align:center">![pwd](resources/img/laptop_lan.jpg)</span>

I am not going to go into too much detail about the network stack, but I will cover some of the basics.

When you connect two machines together, they speak to each other using identifiers called "IP Addresses". IP stands for Internet Protocol. You may have come across these numbers in the past. They can look like `10.0.0.12` or `192.168.1.25`, etc.

## CIDR

IP addresses exist in ranges. This is called the CIDR, or "Classless inter-domain routing", so that you can have multiple networks in one area and be allowed to separate them. For example, a machine with `10.0.0.1` will not normally be able to talk to a machine that has `192.168.0.1`, unless you specify routing.

IPv4 (the most common one still used today) is a 32-bit address space. Each of the dots represents 8 bits. 8bits.8bits.8bits.8bits. 8x4 = 32. You can specify the range of bits that are allowed to communicate with one another through the CIDR notation. It looks like `0.0.0.0/0`. The slash (`/`) at the end represents the amount of bits to shave off of the available IP space.

The number after the slash (`/`) is also known as the netmask.

### CIDR Definition Example

If we knew we wanted to support 254 machines with one IP range in the `10.0.0.x` space, we could specify that as `10.0.0.0/24`, the 24 is subtracted from the 32 bit total, leaving you with 8 bits. 2^8 = 256, but `10.0.0.0` and `10.0.0.255` are reserved IP addresses for the IP protocol, so we ignore those. That leaves us with 256 - 2 = 254 IP addresses. So that's `10.0.0.1 - 10.0.0.254`.

If you wanted to represent the CIDR in netmask (not using the slash), then you could say that the _network_ is `10.0.0.0` and the _netmask_ is `255.255.255.0`. This is because 2^8 (`x/24`, `32-24`) = 256. 0...255 = 256. `255.255.255.0` is `255.255.255.255` minus 256 at the end.

## DHCP

So how does it work when you plug a machine in and it automagically gets an IP address? When you plug your network cable in or connect to the WiFi on your router, a service that runs on your router called the DHCP server will receive a request from your computer asking for an IP. The procedure goes as follows:

- Computer is plugged in or connected to WiFi
- Computer broadcasts over `255.255.255.255` (a reserved IP address), asking for an IP address
- The router's DHCP server responds saying that it has an IP address for the computer
- The computer acknowledges that the DHCP server has an IP address for it and requests that IP
- The router's DHCP server send the IP address to the computer
- The computer assigns itself with the IP address

<span style="display:block;text-align:center">![pwd](resources/img/dhcp.jpg)</span>


# Setting up Networking in Linux

!> For this competition, you need to be able to set a static IP address on all machines that you are working with.

There are three main ways of settings static IP addresses up on machines. It used to be two, but then Ubuntu decided to try to be special and do its own thing as well. I will cover how to set a static IP address on both RHEL-based Linux distributions (CentOS, Fedora, RHEL) and Debian-based ones (Ubuntu, Debian).

As a rule, if you want to get the IP/network interfaces that you have on a machine, or you want to check the IP addresses assigned to the interfaces, you can use the command `ip address`. It will result in something like the following:

```shell
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:16:3e:c0:ee:18 brd ff:ff:ff:ff:ff:ff
    inet 192.168.2.97/24 brd 192.168.2.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::216:3eff:fec0:ee18/64 scope link 
       valid_lft forever preferred_lft forever
```

`lo` and `eth0` are both network interfaces, but `lo` is a virtual interface. It is safe to ignore. `eth0` is a real network interface that can be manipulated.

## Debian-based

### Debian and Ubuntu (version 16.04 and below)

Debian and older Ubuntu distributions use what's called `ifupdown`. It is a set of scripts that manage the bringing up or shutting down of interfaces as well as setting their IP information. The directory `/etc/network/` contains all of the network configuration scripts for these operating systems. `ifupdown` has a bunch of commands that can be used. Here is a quick list of some of the important commands:

| Command | Function |
|---------|-------------|
| `ifup <interface_name>` | Brings an interface with the name `interface_name` up and sets its configuration |
| `ifdown <interface_name>` | Brings an interface with the name `interface_name` down and removes its configuration |
| `ifup -a` | Automatically brings up all interfaces as detailed in the configuration file |

#### The "interfaces" file

In the `/etc/network` directory exists a file called `interrfaces`. This is a key file that contains configuration for settings static or dynamic IP addresses. In there, you will find configuration that looks similar to the following:

> _/etc/network/interfaces:_
```ini
1    # This file describes the network interfaces available on your system
2    # and how to activate them. For more information, see interfaces(5).
3    
4    # The loopback network interface
5    auto lo
6    iface lo inet loopback
7    
8    # The primary network interface
9    auto eth0
10   iface eth0 inet dhcp
```

Everything with a hash/pound (#) in front of it can be ignored. These are comments in the file that are not used as part of the logic. They are purely for additional information.

In line 5 we have two important keywords: `auto lo`
- The `auto` parameter tells the command `ifup -a` to bring this interface up. Interfaces that do not have `auto` specified will be ignored by `ifup -a`. 
- The `lo` parameter is the name of the interface that the auto should apply to. In this specific case, the `lo` interface stands for loopback. **This interface should be ignored and left alone. It is a reference for the operating system that points to itself.**

Line 6 has some more detailed information that controls HOW the interface is configured one it's brought up.
- The `iface` parameter means that you are about to describe configuration details for a network interface.
- The parameter following `iface` is the name of the interface that you want to apply a configuration to.
- The `inet` parameter indicates that this is a _TCP/IP_ network. This is something that should always be there.
- The `dhcp` parameter means that this interface (specified after `iface`) should ask for an IP address instead of setting one statically.

This structure is applied to every interface that you may have on your machine.

