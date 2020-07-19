# Cisco Device Security

The four configuration modes

| Prompt               | Level                                              |
| -------------------- | -------------------------------------------------- |
| hostname>            | User Exec mode indicated by the >                  |
| hostname#            | Privileged Exec mode indicated by the #            |
| hostname(config)#    | Global Config mode indicated by the (config)       |
| hostname(config-if)# | Interface Config mode indicated by the (config-if) |

## Local Static Passwords

Secure access to the console cable line.

```cisco
line console 0
    password <password>
    login
```

To secure access via telnet or ssh. This requires an IP addresses to be assigned first.

```cisco
interface vlan <id>
    ip address x.x.x.x .y.y.y.y
    no shut
    exit
ip default-gateway z.z.z.z
line vty 0 15
    password <password>
    login
```

## Default Timeout Period

By default connections are closed after 10 minutes. To change it do the following.

```cisco
line con 0
    exec-timeout 15
    exit
line vty 0 15
    exec-timeout 5 30
    exit
```

The first timeout is 15 minutes and the second timeout is 5 minutes and 30 seconds. To disable use `no exec-timeout` or `exec-timeout 0`.

## Additional VTY Security

An ACL can be applied to telnet and ssh sessions to further enhance security. The following restrict remote access to a single host.

```cisco
access-list 1 permit host x.x.x.x
line vty 0 15
    access-class 1 in
```

## Privileged and Exec Security

The old command to secure privileged access stores the password in clear text. The command is.

```cisco
enable password <password>
```

To save the password in encrypted form use this command.

```cisco
enable secret <password>
```

Best practice is to use on the the secret and never use the password. Another best practice is to secure the console and VTY passwords. That is achieved using this global config command.

```cisco
service password-encryption
```

Now all passwords are stored in encrypted form.

## Usernames and Privilege Levels

### Usernames
Grant access using individual user accounts. They can be configure as follows.

```cisco
username admin1 secret <password1>
username admin2 secret <password2>
line console 0
    login local
line vty 0 15
    login local
```

Now users will need to enter their specific username and password.

### Privilege Levels

Access can be further restricted using privilege levels.

There are 15 levels but only 3 are used by default.

| Level | Name       | Access                                                         |
| ----- | ---------- | -------------------------------------------------------------  |
|   0   | Zero level | Only allows 5 commands; logout, enable, disable, help and exit |
|   1   | User level | Limited read only commands                                     |
|  15   | Privileged | Provides complete access to the switch or router               |

To set the privilege level for a specific user do the following.

```cisco
user admin1 privilege 15 secret <password>
```

To see what privilege level you have use.

```cisco
sh privilege
```

To configure a command to run at a specific privilege level to allow the creation of limited admins, use the following.

```cisco
privilege exec level 5 show running-config
```

Passwords can also be set on specific privilege levels.

```cisco
enable secret level 5 <password>
```

To log in at a specific level the command ```enable 5```.

## SSH Secure Shell

Telnet is an insecure protocol as traffic is not encrypted and so should not be used. Instead, ssh should be used. To enable ssh and disable telnet do the following.

```cisco
ip domain-name <any domain>
crypto key generate rsa
    1024
user admin1 secret <password>
line vty 0 15
    transport input ssh
    login local
    exit
ip ssh version 2
```

When using ssh, you have to use local user accounts and not a line level password.

## AAA - Authentication, Authorization and Accounting

