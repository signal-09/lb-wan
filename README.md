# LB-Wan

Load Balancer WAN provide a mecanism to load balance multiple Internet ISP connections increasing
 connection reliability and bandwidth.

This first implementation is pure `bash` scripting.

## How?

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
git clone https://github.com/signal-09/lb-wan
cd lb-wan
aclocal && autoconf && automake --add-missing
./configure --prefix=/usr --sysconfdir=/etc --runstatedir=/run
make
sudo make install
```
