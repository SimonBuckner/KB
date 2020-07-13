# Switch & Port Security

These options protect against malicious DHCP and ARP requests.

## DHCP SNooping

DHCP snooping ensures on trusted DHCP servers provide addresses to clients.

```Cisco
  ip dhcp snooping
  ip dhcp snooping vlan 10
  int f0/1
    ip dhcp snooping trust
```

Basically enable for the relevant VLAN and then indicate the port that HCP offers should come from.

## Dynamic ARP Inspection

Dynamic ARP Inspection builds a list of leases provided from the DHCP server and ensures any gratuitous ARP packets that differ from the DHCP traffic are dropped.

```Cisco
  int f0/1
    ip arp inspection trust
    exit
  ip arp inspection vlan 10
```

As with DHCP, trust the port where the DHCP server is connected via.

## Port Security

Filter base don MAC address but restrict to specified number of learned addresses per port. Prevents users from plugging in APs or other switches into access layer switches.

```Cisco
  int range f0/1 - 24
    switchport mode access
    switchport port-security
```

This will allow only one device at any given time on any of the ports. On a violation you would need to `shut`/`no shut` the interface.

To enable auto recovery of the port once a violation has been cleared.

```Cisco
  errdisable recovery cause psecure-violation
  errdisable recovery interval 600
```

Will allow traffic after 600 seconds.

Some other commands.

## Misc Examples

 1. Lock Port to Specific MAC

    ```Cisco
    int f0/1
      switchport mode access
      switchport port-security
      switchport port-security mac-address 1111.2222.3333
    ```

 2. Lock Port to Max MACany Two MAC

    ```Cisco
    int f0/1
      switchport mode access
      switchport port-security
      switchport port-security maximum 2
    ```

 3. View Status of All Ports

    ```Cisco
    sh port-security
    ```

 4. View MAC Addresses

    ```Cisco
    sh port-security address
    ```

 5. View Port Status

    ```Cisco
    sh port-security int f0/1
    ```
