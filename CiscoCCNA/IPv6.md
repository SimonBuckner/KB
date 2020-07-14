# IPv6

IPv6 summary.

- IPv6 addresses are 128 bits long rather than the 32 bits for IPv4 addresses
- They are written as 8 x 16 bit numbers in hexadecimal form, separated by colons
- Leading zeroes can be ignored
- Addresses can be further shortened by replacing adjacent zeroes with a double :
  - An example is 2001:db01:0:1:0:0:0:0:1 can be written as 2001:db01:0:1::1
  - This can only be done once in an address to prevent ambiguity
- Each 16 bit number is referred to as a hexatet, segment, quartet or portion

## Global Unicast Addresses

Overview.

- They are equivalent to public IPv4 addresses
- They are globally reachable by default
- They are assigned from the range 2000::/3
- They are generally provided in /48 blocks
- Smaller ranges are available
- Addresses assigned to an individual host should be assigned a /64 address

The convention for using these /48 subnets is.

- Segments 1-3 are the company
- Segment 4 is the network
- Segment 5-8 are the hosts in the network

## Enable IPv6 on an Interface

```cisco
ipv6 unicast-routing
int f0/0
    ipv6 add 2001:db8::1/64
    no shut
    exit
int f1/0
    ipv6 add 2001:db8:0:1::1/64
    no shut
```

## Extended Unique Identifier (EUI-64) Addresses

Automatically derived addresses can make configuring IPv6 addresses easier. The host portion of the address is arrived at using the following process. **It is not recommended for router addresses**.

- Take the MAC addresses
- Insert FF:FE in the middle
- Invert the 7th bit

For example and interface with the MAC address ca01.2f24.0000 would become 2001:db8:0:0:c801:2fFF:FE24:0.

Can be configured as follows.

```cisco
ipv6 unicast-routing
int f0/0
    ipv6 add 2001:db8:0::/64 eui-64
    no shut
```

## Unique Local IPv6 Addresses

Summary.

- Similar to RFC-1918 addresses and not publicly reachable
- Assigned from the FC00::/7 range
- Hosts should be assigned /64 addresses

## Link Local IPv6 Addresses

Summary.

- Valid only for communications on the local link
- Assigned from the FE80::/10 range
- Hosts should be assigned /64 addresses
- Essentially ultra-private addresses
  - They are never routed
- A link local address is mandatory on all IPv6 enabled interfaces
- Used for traffic that should not be routed, e.g. dynamic routing hello packets

The address is automatically configured for each interface with a EUI-64 address. If you want to manually configure a link local address, the command are.

```cisco
ipv6 unicast-routing
int f0/0
    ipv6 fe80::1 link-local
    no shut
int f0/1
    ipv6 fe80::1 link-local
    no shut
```

Due to the traffic not being routed, the same link local address can be used on all interfaces.
