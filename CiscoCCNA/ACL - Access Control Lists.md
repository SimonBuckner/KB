# Access Control Lists

Access control lists are used by various features of the Cisco device to select or restrict
traffic based on various characteristics like source address, destination port, protocol type.
The two main flavours are numbered and named. Within each type you can have standard or extended
lists. Standard lists only allow selection based on the source address whereas extended allow
you to select on both ends of the communication.

## Numbered Access Control Lists

- Access Control Lists are made up of individual Access Control Entries or ACLs and ACEs.
- Each interface can have zero or one inbound ACL
- Each interface can have zero or one outbound ACL

ACLs can be numbered or named and come in three flavours, standard, extended and expanded.

|  ACL Type | Number ranges |
| --------- | ------------ |
| Standard  |    1-99 and 1300-1999 |
| Extended  |  100-199 and 2000-2699 |

In addition, standard ACLs only specify the source host or subnet, where as the others can select both source and destination for the host, subnet, protocol, port and handshake stage for connection oriented protocols.

Rules are evaluated from top to bottom and the first rule that is matched is used, further rules are processed. For any ACL, there is always an implicit `deny any any` rule at the end.

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

## Named Access Control Lists

Examples of standard named ACLs.

1. Block a single host to a single host for telnet inbound on interface f0/1

    ```cisco
    ip access-list extended F0/1_in
      deny tcp host 10.1.0.1 host 10.2.0.1 eq 23
      exit
    int f0/1
      ip access-group F0/1_in inbound
    ```

2. Block a subnet from accessing telnet on a particular host on interface f0/1

    ```cisco
    ip access-list extended F0/1_in
      deny tcp 10.1.0.0 0.0.0.255 host 10.2.0.1 eq 23
      exit
    int f0/1
      ip access-group F0/1_in inbound
    ```

3. Permit ping from one host to another on interface f1/1

    ```cisco
    ip access-list extended f1/1_out
      permit icmp host 10.1.0.1 host 10.2.0.1 echo
      exit
    int f1/1
      ip access-group F1/1_out outbound
    ```

4. Next


## Numb