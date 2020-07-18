# VPN - Virtual Private Networks

Quick how to on setting up a site-to-site VPN.

The first step is to get the two routers to trust and authenticate each other.

```cisco
crypto isakmp policy 1
    encryption aes
    hash sha
    authentication pre-share
    group 2
    lifetime 86400
    crypto isakmp key <pre-share-key> <remote-router-public-ip>
    exit
```

Then define which traffic should travel across the VPN.

```cisco
ip access-list extended myVPN-ACL
    permit ip <local subnet> <local netmask> <remote subnet> <remote netmask>
```

*Note: The netmask are standard ACL search masks. E.g. 255.255.255.0 would be written 0.0.0.255.

Next step is to define the encryption used to secure the traffic, and which traffic to secure.

```cisco
crypto ipsec transform-set myTS esp-aes esp-sha-hmac
crypto map myCM 10 ipsec-isakmp
    set peer <remote-router-ip>
    set transform-set myTS
    match address myVPN-ACL
    exit
interface 0/1
    crypto map myCM

```

The final step is to ensure traffic going to the internet is NATed and traffic going to the VPN is not.

```cisco
ip access-list extended myNAT-ACL
    deny ip <local subnet> <local netmask> <remote subnet> <remote netmask>
    permit ip <local subnet> <local netmask> any
```

This ACL will need to be used on the NAT rules configured elsewhere.
