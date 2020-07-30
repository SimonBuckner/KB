# Wi-Fi Wireless Networking

## Network Types

The three types of wireless networks are.

- WPAN  - Wireless Personal Area Network
        - Up to 10 metres
        - Often Blue Tooth
- WLAN  - Wireless Local Area Network
        - Up to 100 metres
        - Provides wireless to a campus or building
- WMAN  - Wireless Metropolitan Area Network
        - Covers a large city area

### WLAN Network Modes

The two modes of WLAN are ad-hoc and infrastructure.

- ad-hoc         - wireless stations communicate in a peer-to-peer method
- infrastructure - stations communicate with fixed wireless access points

There are extensions to the infrastructure mode we are.

- WiFi Direct  - Allows a single point-to-point connection to a screen or other WiFi Direct device
- WPS          - WiFi Protected Setup allows device setup by pressing a button

The WiFi Direct services are.

- DLNA     - Digital Living Network Alliance which allows devices to stream media to each other
- Miracast - Which allows connections to external monitors
- Print    - Which allows printing

### Access Point Modes

Finally there are Wireless Bridges and Mesh WiFi.

- Bridges - Connect areas via WiFi that are not connected by physical networks
- Mesh    - These utilise multiple radios, one to provide connectivity to clients and one to bridge between AP's

## Infrastructure Mode and Wireless Access Points

- Basic Service Set            - A devices operating in infrastructure mode is called a BSS
- Distribution Systems         - These are the switches that AP's are connected to
- Basic Service Set Identifier - The BSSID use the device MAC address to identify devices in a BSS
- Basic Service Area           - The BSA is the wireless coverage of the Access Point, also known as wireless cell
- Service Set Identifier       - The SSID is a unique identifier that names the wireless network
                               - An AP can support multiple SSID's with different SSID's, security settings and network access
- Beacons                      - AP's broadcast their SSID using beacons
                               - This can be turned off although it provides minimal security
- Extended Service Set         - Multiple access points with the same SSID across a wider area are called an ESS
- Roaming                      - Devices can move between AP's in the same WLAN/ESS

## Wireless LAN Controllers (CAPWAP)

WLAN Controllers (WLC) centralise the management of access points into a central location which aids in managing a large number of access points. AP's managed individually are called Autonomous AP's and those controlled by a controller are called Light Weight AP's.

Light Weight AP's can be setup using Zero Touch Configuring. These devices can discover the location of the controller using DHCP or DNS.

- DHCP  - Use option 43 is used to configure the controller
- DNS   - Use the host `cisco-capwap-controller` in the local zone
- Local - Broadcast on the local subnet

The controller will set which SSID's each AP should broadcast and also control channel and other wireless settings. The controller can also detect rouge AP's. Controllers also improve roaming by sharing the authentication domain.

## Control and Provisioning of Wireless Access Points (CAPWAP)

- Standardised protocol that is used to configure and control AP's
- Communications are encrypted in a DTLS CAPWAP tunnel
- Used UDP ports 5246 and 5247 for communications

When AP's operate in light weight mode, some operations are handled by the controller and others are handled by the AP. This is referred to as Split MAC.

### Split Mac - Access Point Operations

- Client handshake when connecting
- Beacons
- Performance monitoring
- Encryption/decryption
- Clients power save

### Split Mac - Controller Operations

- Authentication
- Roaming control
- 802.11 to 802.3 communication
- Radio frequency management
- Security management
- QoS management

### Split Mac - Traffic Path

- With autonomous AP's, traffic goes from the device to the AP to the distribution system
- With controllers, the traffic goes from the device to the AP to the controller (via CAPWAP tunnel) to the distribution system

This means the controller must be able to handle all the traffic from all the AP's. EtherChannel/LAG will often be used to connect the WLC to the distribution switch to handle the increased traffic load.

## FlexConnect

This is used for AP's in remote offices where the controller is not on the local site. This allows the light weight AP to handle local traffic without sending it via the CAPWAP tunnel.

## Switch Configurations

### Switch Config for Autonomous AP's

The port the AP is connected to needs to support all the VLAN's used by the SSID's.

```cisco
vlan 21
    name CORPROATE
vlan 22
    name GUEST
int GigabitEthernet1/0/1
switchport trunk encap dot1q
switchport mode trunk
switchport trunk allowed vlan 21,22
```

### Switch Config for Light Weight AP's

For light weight AP's the VLAN tagging is done on the controller port. The two additional VLAN's required are.

- WLC Management - Allows admin access to the WLC to manage it
- AP Management  - Allows the WLC to manage the AP's and for the CAPWAP tunnel traffic

These two VLAN's could be merged into one VLAN but this is best practice.

```cisco
vlan 10
    name WLC_Management
vlan 11
    name AP_Management
vlan 21
    name CORPROATE
vlan 22
    name GUEST
int GigabitEthernet1/0/2
switchport trunk encap dot1q
switchport mode trunk
switchport trunk allowed vlan 10,11,21,22
!
int GigabitEthernet1/0/1
switchport mode access
switchport trunk allowed vlan 11
```
