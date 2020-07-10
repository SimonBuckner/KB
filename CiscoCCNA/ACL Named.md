# Access Control Lists - Named

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
