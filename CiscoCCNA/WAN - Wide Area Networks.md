# WAN - Wide Area Networks

WANs connect network across geographically separated locations

## VPN - Virtual Private Networks

Site-to-Site tunnel types.

- IPsec - Open standard IPsec tunnel, does not support multicast
- GRE (Generic Routing Encapsulation) over IPsec - Add support for multicast
- IPsecVTI (Virtual Tunnel Interface) - Cisco proprietary protocol. GREoIPsec normally used
- DMVPN (Dynamic Multi-point VPN) - Cisco proprietary protocol. Configured hub & spoke. Remote sites learn from hub how to connect to each other in a mesh.
- FlexVPN: Another Cisco. Similar to and newer than DMVPN
- GETVPN (Group Encrypted Transport VPN) - Yet more Cisco. Scalable centralised policy  for VPN over non-public links. E.g. MPLS

## WAN Connection Options

The primary options, according to Cisco, are.

- Leased lines
- MPLS (Multi Protocol Label Switching)
- Satellite
- Optical fibre - Fibre to the ?? (FttX)
  - FttH - Home
  - FttP - Premise
  - FttB - Building
  - FttN - Neighborhood

Fibre standards; SONET in North America and SDH for the rest of the world. Used mainly for service provider back end networks.

| SONET STS | SONET OC  | SDH STM | Bit Rate Mbps |
| --------- | --------- | ------- | ------------: |
| STS-1     | OC-1      |         |        54.84  |
| STS-3     | OC-3      |  STM-1  |       155.52  |
| STS-12    | C-12      |  STM-4  |       622.08  |
| STS-48    | C-48      | STM-16  |      2488.32  |
| STS-192   | OC-192    | STM-64  |      9953.28  |

## MPLS Multi Protocol Label Switching

Summary.

- WAN connectivity provided over MPLS infrastructure is normally provided by a Service Provider
- MPLS links normally transport data from multiple customers
- The traffic is strictly segregated so the traffic is secure
- MPLS networks are mesh
- Each connected site has a unique network ID

Node terminology.

- CE - Customer Edge device
- PE - Provider Edge device
- P  - Provider core device

Only the PE and P devices handle MPLS traffic. The CE equipment has no knowledge of the MPLS. The P devices are completely transparent to the CE devices.

### Layer 2 MPLS

It is possible to have a layer 2 MPLS. In this instance, the CE devices do not peer at layer 3 with the PE devices. The PE devices appear switch like to the CE devices and both ends of the MPLS use the same network ID. The CE devices could be switches and not routers where as with the above layer 3 MPLS topology routers are required.

Some reasons for a layer 2 MPLS would be an load balanced application that require all nodes to be in the same subnet, however the nodes are geographically separated for business continuity reasons.

Also know as.

- VPLS (Virtual Private LAN Service) - Multipoint Layer 2 VPN
- VPWS (Virtual PseudoWire Service) - Point to Point  Layer 2 VPN

## WAN Topology Options

