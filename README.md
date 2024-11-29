# LB-Wan

Load Balancer WAN provide a mecanism to load balance multiple Internet ISP connections increasing
 connection reliability and bandwidth.

This first implementation is pure `bash` scripting.


## How it works

LB-Wan use `iproute2` [Multiple Route Tables](http://www.policyrouting.org/iproute2.doc.html#ss9.16)
feature to segregate every WAN/ISP routes in a separate routing namespace.

Segregation is made by [Virtual Routing and Forwarding (VRF)](https://docs.kernel.org/networking/vrf.html).

[Load Balancing routing](https://lartc.org/howto/lartc.rpdb.multiple-links.html) is composed with
active WAN/ISP instances in order to ensure reliable connections.


## Prerequisites

At the moment LB-Wan relies on:
* A `systemd` GNU/Linux environment
* `iptables` Linux [netfilter project](https://www.netfilter.org)
* `ifupdown` networking tools (`netplan` is coming, as well as `NetworkManager`)
* [ISC DHCP Client](https://www.isc.org/dhcp/)


## Installation

```shell
> git clone https://github.com/signal-09/lb-wan
> cd lb-wan
> aclocal && autoconf && automake --add-missing
> ./configure --prefix=/usr --sysconfdir=/etc --runstatedir=/run
> make
> sudo make install
```

Install the following dependencies:

```shell
> apt-get install isc-dhcp-client
```


## Configuration


### Networking

Suppose a Debian based router with 4 network cards:

```
                                            -- eth1 --- FTTH --
                                           /                   \
                               +----------+                     \
  192.168.0.254/24 --- eth0 ---|  Router  |--- eth2 --- FTTC ----+--> Internet
                               +----------+                     /
                                           \                   /
                                            -- eth3 --- FWA ---
```

The system network configuration file `/etc/network/interfaces` has the following content:

```
# The loopback network interface
auto lo
iface lo inet loopback

# Internal network
auto eth0
iface eth0 inet static
    address 192.168.0.254/24
    dns-nameservers 8.8.8.8 8.8.4.4

# FTTH internet provider
auto eth1
allow-hotplug eth1
iface eth1 inet dhcp

# FTTC internet provider
auto eth2
allow-hotplug eth2
iface eth2 inet dhcp

# FWA internet provider
auto eth3
allow-hotplug eth3
iface eth3 inet dhcp
```


### Service

Create or edit the `/etc/lb-wan.conf` configuration file to reflect desired balanced connections:

```
# service	device	weight	ping-ips
FTTH		eth1	50	8.8.8.8
FTTC		eth2	30	8.8.8.8
FWA		eth3	20	8.8.8.8
LAN		eth0
```

The balanced connection will result in:
* 50% FTTH via `eth1`
* 30% FTTC via `eth2`
* 20% FWA via `eth3`

IP `8.8.8.8` is used as ping target to check connection readiness.

Reserved service name `LAN` identify the internal network. `dns-nameservers` from internal network
will be used as balanced DNS servers.

Default values can be tuned by editing `/etc/default/lb-wan` file:

```
# Indicates how often to check the ping host for each ISP in seconds.
CHECK_INTERVAL=3

# How long ping are allowed to respond before consider unreachable.
PING_TIMEOUT=2

# Define the maximum packet losses required to declare a link up or down.
MAX_PACKET_LOSS=5

# Network manager (auto|ifupdown|netplan|manual)
NETWORKING=auto

# Comma or space separated DNS servers
#NAMESERVERS="0.0.0.0"

# Allow LB-Wan to manage iptables rules (e.g. FORWARD and NAT)
#IPTABLES=yes

# Setup IPv6 (EXPERIMENTAL)
#IPV6=no
```


### Usage

#### Start the service:

```shell
> sudo systemctl start lbw-vrf
> sudo systemctl start lbw-balancer
OR
> sudo lbw service start
```

Routing table will change as follows:

```shell
> ip route show
default 
	nexthop via 172.16.1.1 dev eth1 weight 50
	nexthop via 172.16.2.1 dev eth2 weight 30
	nexthop via 172.16.3.1 dev eth3 weight 20
192.168.0.0/24 dev eth0 proto kernel scope link src 192.168.0.254 
```

For debugging purpose every service can be started in foreground (`-f`) or in debugging mode (`-d`):

```shell
> sudo lbw -f -t service start vrf
Nov 29 17:31:04 lbw-vrf[3791029]: net.ipv4.tcp_l3mdev_accept = 1
Nov 29 17:31:04 lbw-vrf[3791029]: net.ipv4.udp_l3mdev_accept = 1
Nov 29 17:31:04 lbw-vrf[3791029]: net.ipv4.ip_forward = 1
Nov 29 17:31:04 lbw-vrf[3791029]: Natting interface eth0 from 192.168.0.0/24
Nov 29 17:31:04 lbw-vrf[3791029]: Natting interface eth1 from 192.168.0.0/24
Nov 29 17:31:04 lbw-vrf[3791029]: Natting interface eth2 from 192.168.0.0/24
Nov 29 17:31:04 lbw-vrf[3791029]: Natting interface eth3 from 192.168.0.0/24
Nov 29 17:31:22 lbw-vrf[3791029]: Declaring FTTH UP
Nov 29 17:31:22 lbw-vrf[3791029]: Declaring FTTC UP
Nov 29 17:31:23 lbw-vrf[3791029]: Declaring FWA UP
```

```shell
> lbw -f -t service start balancer
Nov 29 17:59:00 lbw-balancer[3815641]: VRF service become available
Nov 29 17:59:00 lbw-balancer[3815641]: Set default route for IPv4 to nexthop via 172.16.1.1 dev eth1 weight 50 nexthop via 172.16.2.1 dev eth2 weight 30 nexthop via 172.16.3.1 dev eth3 weight 20
```

#### Stop the service:

```shell
> sudo systemctl stop lbw-balancer
> sudo systemctl stop lbw-vrf
OR
> sudo lbw service stop
```


## Suggestions

1) Do not use Modem IPs as DNS server: WAN/ISP with down link may not be able to resolve DNS names
   and may cause delay or timeout in name resolutions;
