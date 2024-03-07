# DHCP option injector
[![Build Status](https://travis-ci.org/misje/dhcpoptinj.svg?branch=dev)](https://travis-ci.org/misje/dhcpoptinj) [![Total alerts](https://img.shields.io/lgtm/alerts/g/misje/dhcpoptinj.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/misje/dhcpoptinj/alerts/)

Have you ever wanted to intercept DHCP requests and squeeze in a few extra DHCP
options, unbeknownst to the sender? Probably not. However, should the need ever
come, **dhcpoptinj** will (hopefully) help you.

## Why

There can be many a reason to mangle DHCP requests, although chances are you
ought to look for a much better method for solving your problem. Perhaps you do
not have access to the DHCP server/clients and need to modify their DHCP
options, perhaps the DHCP software is difficult to configure (or does not
support what you want to do), perhaps you have a very complex and/or odd setup,
or perhaps you just want to experiment sending exotic or malformed options?
There is a small chance that dhcoptinj might actually be of some use.

## How

dhcpoptinj waits for packets to arrive in a netfilter queue. It will ensure
that a packet is in fact a BOOTP/DHCP packet, and if so proceed to inject
options. It will recalculate the IPv4 header checksum, disable the UDP
checksum (for a simpler implementation) and then give the packet back to
netfilter.

You need an iptables rule in order to intercept packets and send them to
dhcpoptinj. Let us say you have two interfaces bridged together, *eth0* and
*eth1*. Let us say you want to intercept all BOOTP requests coming from *eth0*
and inject the [relay agent information
option](https://tools.ietf.org/html/rfc3046) (82/0x52). Let us make up a silly
payload: An [agent circuit ID
sub-option](https://tools.ietf.org/html/rfc3046#section-3.1) with the value
"Fjas".

Add a rule to the iptables mangle table:`sudo iptables -t mangle -A PREROUTING
-m physdev --physdev-in eth0 -p udp --dport 67 -j NFQUEUE --queue-num 42`.

Then run dhcpoptinj (let us run it in the foreground with extra debug output):
`sudo dhcpoptinj -d -f -q 42 -o'52 01 04 46 6A 61 73'`. Note that dhcpoptinj
must be run by a user with the CAP\_NET\_ADMIN capability. You do not need to,
and you really should not run dhcpoptinj as root. Instead, you can for instance
grant the CAP\_NET\_ADMIN capability to the binary (using *setcap*) and limit
execution rights to only a specific user or group. This is a method used for
running wireshark as non-root, so you will find several guides helping you
accomplish this.

Now send a DHCP packet to the *eth0* interface and watch it (using a tool like
[wireshark](https://www.wireshark.org/)) having been modified when it reaches
the bridged interface. It should have the injected option at the end of the
option list. If you capture the incoming DHCP packet with Wireshark, it will
appear unmodified although it will in fact be mangled.

Note the format of the argument to the *-o* option: It should be a hexadecimal
string starting with the DHCP option code followed by the option payload. The
option length (the byte that normally follows the option code) is automatically
calculated and must not be specified. The hex string can be delimited by
non-hexadecimal characters for readability. All options must have a payload,
except for the special [pad
option](https://tools.ietf.org/html/rfc2132#section-2) (code 0).

The layout of the nonsensical option used in this example (first the [DHCP
option layout](https://tools.ietf.org/html/rfc2132#section-2), then the
specific [relay agent information option sub-option
layout](https://tools.ietf.org/html/rfc3046#section-2.0)) is as follows:

| Code | Length |            Data            |
|------|--------|----------------------------|
|  52  | (auto) | 01 04 46 6A 61 73 ("Fjas") |

| Sub-opt. | Length |         Data         |
|----------|--------|----------------------|
|    01    |   4    | 46 6A 61 73 ("Fjas") |

Note that dhcpoptinj does not care about what you write in the option payloads,
nor does it check whether your option code exists. It does however forbid you
to use the option code 255 (the terminating end option). dhcpoptinj inserts
this option as the last option automatically.
