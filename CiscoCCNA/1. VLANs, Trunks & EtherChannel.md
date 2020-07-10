
## VLAN Trunks

## Spanning Tree
The two core switches should be configured to ensure they are the root of the STP tree for each VLAN.

On the primary core switch.

`spanning-tree vlan 1 root primary`

On the secondary core switch.

`spanning-tree vlan 1 root secondary`

That needs repeating for each VLAN that is active.

All access layer ports should be configured for PortFast and BPDUGuard to bring them up quicker and protect agianst switches that broadcast BPDU packets.

`int range f0/1 -24
  spanning-tree portfast
  spanning-tree bpduguard enable`



