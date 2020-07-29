# Network Address Translation

## NAT Types

The three types covered in the CCNA course are Static, Dynamic and Port.

- Static is a one-to-one relation between the public IP address and the private IP address, often used for servers
- Dynamic provides a pool of addresses but is limited as each public IP address is allocated to a private host in a one to one relationship
- Port allows ofr multiple private hosts to use the same public host with the source port being translated as well

## Static NAT

The following configures a static NAT rule mapping a single public IP to single private IP. this assumes that F0/0 in the external
interface and F1/0 is the internal interface. This rule is bi-directional so works for traffic in both directions.

```cisco
int f0/0
    ip address 203.0.113.2 255.255.255.240
    ip nat outside
    exit
int f1/0
    ip address 10.0.0.1 255.255.255.0
    ip nat inside
    exit
ip nat inside inside sources static 10.0.0.10 212.113.101.3
```

## Dynamic Network Address Translation

Dynamic, one-to-one NAT. No connections once all public IPs used. Not really used in the real world.

```cisco
int f0/0
    ip address 203.0.113.2 255.255.255.240
    ip nat outside
    exit
int f1/0
    ip address 10.0.0.1 255.255.255.0
    ip nat inside
    exit
access-list 1 permit 10.0.0.0 0.0.0.255
ip nat pool DynamicPIPs 203.0.113.4 203.0.113.14 netmask 255.255.255.240
ip nat inside source list 1 pool DynamicPIPs
end
```

## Port Address Translation (Multi-Address)

PAT is almost identical to standard dynamic NAT, with PAT though, the last IP address in the public range will be re-used for additional private hosts with source port translation.

```cisco
int f0/0
    ip address 203.0.113.2 255.255.255.240.
    ip nat outside
    exit
int f1/0
    ip address 10.0.0.1 255.255.255.0
    ip nat inside
    exit
access-list 1 permit 10.0.0.0 0.0.0.255
ip nat pool DynamicPIPs 203.0.113.4 203.0.113.14 netmask 255.255.255.240
ip nat inside source list 1 pool DynamicPIPs overload
end
```

## Port Address Translation (Small office, single dynamic IP Address)

Works for small offices with public IP address obtained via DHCP.

```cisco
int f0/0
    ip address 203.0.113.2 255.255.255.240
    ip nat outside
    exit
int f1/0
    ip address 10.0.0.1 255.255.255.0
    ip nat inside
    exit
access-list 1 permit 10.0.0.0 0.0.0.255
ip nat inside source list 1 interface f0/0 overload
end
```

## Verification Commands

| Command | Description |
| sh ip nat translation | List all entries in the NAT table |
| clear ip nat translation | Clear entries in the NAT table. Use before making changes |
| clear ip nat translation * | Clear dynamic NAT entries |
| sh ip nat statistics | Shows details on how many entries have been translated |

Output from the SH command has the following meaning.

The columns have the following meanings.

| Column         | Meaning |
| -------------- | ------- |
| Pro            | The protocol of the enter. E.g. icmp, tcp, udp |
| Inside Global  | The NATed address of the local device as seen by devices on the outside |
| Inside Local   | The actual address configured on the local device |
| Outside Local  | The IP address of the external host is it appears on their inside network |
| Outside Global | The NATed address of the external host |

The two outside columns generally show the public IP address of the remote host. The only time you would see different
is when joining two networks with the same IP range via a double NAT.
