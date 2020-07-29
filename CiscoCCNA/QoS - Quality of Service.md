# QoS - Quality of Service

The main driver for QoS was to support Voice over IP (VoIP) services.

Network quality has the following requirements for Voice and SD Video

- Latency < 150ms  - The delay for a packet to arrive at the destination
- Jitter < 30ms    - The variation in delay between individual packets
- Packet Loss < 1% - Cannot lose more than 1 packet in 100

By default, all switches and routers operate a First in, First Out (FIFO) queue for all packets. QoS allows packets to jump the queue to provide improved service to traffic of a higher priority. Although QoS can fix bandwidth congestion it is not a long term solution, it is only a temporary fix.

## Classification and Marking

For QoS to work, different traffic flows have to be identified and marked so that the router or switch can act appropriately. The main methods are below.

- Class of Service (CoS)                       - Layer 2
- Differentiated Service Code Point (DSCP)     - Layer 3 (Preferred method)
- Access Control List (ACL)                    - Layer 3 & 4
- Network Based Application Recognition (NBAR) - Layer 3 to 7

### Class of Service

- There is a 3 bit field in the layer 2 802.1q frame header which is used to carry the CoS marking
- The default value of the field is 0 which is best effort traffic
- Values 6 & 7 are reserved for network use like routing protocols and network control traffic
- Values 3 and 5 are used for SIP Signalling and SIP payload respectively
- The higher the number, the more important the traffic is.

### Differentiated Service Code Point

- There is a byte field in the layer 3 header, this is called the Type of Service (ToS) field
- 6 of the 8 bits are used giving a range of 64 possible ToS markings
- The default value again is 0 which is best effort
- Default value for IP phones is 24 (CS3) and 48 (EF) for signalling and payload
- Other standard markings are 26 (AF31) for mission critical data and 34 (AF41) for SD video
- This is the preferred method as the router only needs to decode a single byte

### - Access Control List

- Used to mark traffic from applications that are not QoS aware
- If used should be as close to the traffic source as possible

### Network Based Application Recognition

- Can recognise traffic based on various characteristics
- Can use signatures to categorise traffic
- Signatures can be downloaded from Cisco on a regular basis
- Also write your own signatures
- If used should be as close to the traffic source as possible

### Trust Boundary

To ensure that only markings from trusted sources should be used. Markings from sources that are not trusted should be ignored. This prevents malicious users abusing your QoS rules.

## Congestion Management

Queuing can be used to manage congestion. The two made queuing methods are.

1. CBWFQ - Class Based Weighted Fair Queuing
           Provides a guaranteed amount of bandwidth to specified traffic types
2. LLQ   - Low Latency Queuing
           Provides priority to specified traffic types

## Configuration Overview

Cisco uses MQC module QoS CLI to configure QoS. It has three main sections.

1. Class Map        - Define the traffic to take action on
2. Policy Maps      - Define what action to perform on the traffic
3. Service Policies - Applies to Class/Policy combination to an interface

For this example, two sites are connected via a 768KB link, of which 256 needs allocating for voice.

### LLQ Example

```cisco
class-map VOICE_PAYLOAD
    match ip dscp ef
class-map VOICE_SIGNAL
    match ip dscp cs3
!
policy-map WAN-EDGE
    class VOICE-PAYLOAD
    priority percent 33
    class VOICE-SIGNAL
    priority percent 5
    class class-default
    fair-queue
!
interface Serial0/0/0
    bandwidth 768
    service-policy out WAN-EDGE
```

In the above example, the payload is guaranteed 33% of the bandwidth, but also limited to 33% of the bandwidth. Adding the `default-class` section applies fair queue, rather than FIFO, to the remaining traffic. That remaining traffic can burst up if no voice traffic is using it bandwidth.

## Policing and Shaping

Shaping and policing are alternatives to queuing and are used to limit traffic beyond a certain rate. Shaping will buffer any excess traffic, whilst policing will drop or re-mark excess packet to enforce the limit. Policing will often be used at the service provider edge devices and shaping will be used on customer equipment.

Policing can also be done inside an enterprise to police malicious traffic like worms and file sharing. Best practice it to mark DSCP 8 (CS1). This traffic is known as scavenger traffic.

### Shaping Example

Limit outbound traffic on a WAN router to match the bandwidth limit of the upstream service provider.

```cisco
policy-map EDGE-WAN
    class class-default
    shape average 10000000
!
interface fa0/1
    service-policy out WAN-EDGE
```

## Shaping Example With Nested Queues

```cisco
class-map VOICE
    match ip dscp ef
class-map VIDEO
    match ip dscp af41
class-map SIGNALLING
    match ip dscp cs3
!
policy-map EDGE-WAN-QUEUES
    class VOICE
        priority 1024
    class VIDEO
        priority 3072
    class SIGNALLING
        priority 128
    class class-default
        fair-queue
!
policy-map EDGE-WAN
    class class-default
    shape average 10000000
    service-policy EDGE-WAN-QUEUES
!
interface fa0/1
    service-policy out WAN-EDGE
```
