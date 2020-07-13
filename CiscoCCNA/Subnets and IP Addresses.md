# IP Addresses and Subnets

A quick overview of all things address related.

## IPv4 Address Classes

The IPv4 address space was divided into address classes. These seemed reasonable at the time but they never counted on it being so damn popular! This has led to issues with a lack of available addresses which was part of the driving factor behind IPv6. The classes are.

| Class | 1st Bits | Subnet Mask | Range                       | Networks  | Hosts/Network |
| :---: | :------: | :---------: | --------------------------- | --------: | ------------: |
|   A   |   0xxx   |     /8      |  10.0.0.0 - 126.0.0.0       |       126 |    16,777,214 |
|   B   |   10xx   |     /16     | 128.0.0.0 - 191.255.255.255 |    16,384 |        65,534 |
|   C   |   110x   |     /24     | 192.0.0.0 - 223.255.255.255 | 2,097,152 |           254 |
|   D   |   111x   |     N/A     | 224.0.0.0 - 239.255.255.255 |           |               |
|   E   |   1111   |     N/A     | 240.0.0.0 - 255.255.255.255 |           |               |

The last two classes have special uses.

- Class D - These are multi-cast addresses used to send a single stream of packets to multiple hosts in an efficient manner
- Class E - These are experimental and reserved for future use

## Subnet Masks
