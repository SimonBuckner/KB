# Access Control Lists - Numbered

- Access Control Lists are made up of individual Access Control Entries or ACLs and ACEs.
- Each interface can have zero or one inbound ACL
- Each interface can have zero or one outbound ACL

ACLs can be numbered or named and come in three flavours, standard, extended and expanded.

|  ACL Type | Number ranges |
| --------- | ------------ |
| Sandard   |    1-99 and 1300-1999 |
| Extended  |  100-199 and 2000-2699 |

In addition, standard ACLs only specifiy the source host or subnet, where as the others can select both source and destination for the host, subnet, protocol, port and handshack stage for connection oriented protocols.

Rules are evaluated from top to bottom and the first rule that is matched is used, further rules are processed. For any ACL, there is always an inplicit `deny any any` rule at the end.

## Standard ACL

Configured using the following commands.

Apply an outbound ACL on an interface.

```Cisco
  access-list 1 deny 10.0.1.0 0.0.0.255
  access-list 1 permit 10.0.0.0 0.0.0.255
  int f0/1
    ip access-group out 1
```

Apply an outbound ACL on an interface.

```Cisco
  access-list 1 deny 10.0.1.0 0.0.0.255
  access-list 1 permit 10.0.0.0 0.0.0.255
  int f0/1
    ip access-group out 1
```

## Extended ACL

Permit telnet from a single host whilst blocking telnet from all others. Also allow all other traffic.

```Cisco
  access-list 100 permit tcp host 10.0.0.1 host 10.0.0.2 eq 23
  access-list 100 deny tcp 10.0.0.0 0.0.0.255 host 10.0.0.2 eq 23
  access-list 100 permit any any
  int f0/1
    ip access-group int 100`
```
  