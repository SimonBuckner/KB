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

## User Names and Privilege Levels

### User Names

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

AAA allows for centralised management of accounts that are allowed to access routers and switches. The two protocols/server types for providing centralised security are RADIUS or TACACS+.

- RADIUS is an open source protocol but does not have extensions to allow fine grained control
  over privilege level settings. This is often used for end user access like VPNs
- TACACS+ does allow fine grained control. Permission levels can be granted on a command/user basis.

The Cisco AAA server is ISEIdentity Services Engine. This has replaced the end of life Access Control Server ACE.

Basic AAA configuration looks like this. This is the legacy configuration before the current command.

### Legacy RADIUS Commands

Always configure a backup/break glass account in case AAA is down. You can also specify and optional server groups which can direct different authentication tasks to different servers. E.g. You could direct VPN authentication to the first group and admin access to Cisco devices to another group of servers.

#### Basic Config

```cisco
username BreakGlass secret <password>

aaa new-model
radius-server host x.x.x.1 key <Shared Key1>
radius-server host x.x.x.2 key <Shared Key2>
aaa authentication login default group radius local
```

#### Basic with Server Group

```cisco
username BreakGlass secret <password>

aaa new-model
radius-server host x.x.x.1 key <Shared Key1>
radius-server host x.x.x.2 key <Shared Key2>
aaa group server radius <Group>
    server x.x.x.1
    server x.x.x.2
    exit
aaa authentication login default group <Group> local
```

### RADIUS Current Commands

The legacy commands are being deprecated and instead the following commands should be used.

```cisco
username BreakGlass secret <password>

aaa new-model
radius server Server1
    address ipv4 x.x.x.1
    key <Shared Key1>
    exit
radius server Server2
    address ipv4 x.x.x.2
    key <Shared Key2>
    exit
aaa group server radius RadiusGrp
    server name Server1
    server name Server2
    exit
aaa authentication login default group RadiusGrp local
```

### Legacy TACACS+ Commands

Every thing that applies to Radius also applies to TACACS+.

```cisco
username BreakGlass secret <password>

aaa new-model
tacacs-server host x.x.x.1 key <Shared Key1>
tacacs-server host x.x.x.2 key <Shared Key2>
aaa group server tacacs+ TacacsGrp
aaa authentication login default group TacacsGrp local
```

### Current TACACS+ Commands

The legacy commands are being deprecated and instead the following commands should be used.

```cisco
username BreakGlass secret <password>

aaa new-model
tacacs server Server1
    address ipv4 x.x.x.1
    key <Shared Key1>
    exit
tacacs server Server2
    address ipv4 x.x.x.2
    key <Shared Key2>
    exit
aaa group server tacacs+ TacacsGrp
    server name Server1
    server name Server2
    exit
aaa authentication login default group TacacsGrp local
```

## Global Security Best Practices

These are best practices for global security commands to be entered on all routers and switches.

## Login and Exec Banners

This allows you to display a security warning on before login and once they have logged in.

To display an message before someone logs in.

```cisco
banner login "
The text you want to display.
"
```

To display a message once some has logged in.

```cisco
exec login "
Text to display after login.
"
```

In each case the '"' is the delimiter to indicate the start and the end of the message. Any character can be used.

## Disable Unused Services

To reduce the attack surface area is to disable services that are no required. Always disable the HTTP service as most devices should not be configured via HTTP. Also, in secure environments you may want to disable CDP.

```cisco
no ip http server
no ip https server
no cdp run
```

## Time Synchronization

This is important for authentication and logging. Routers can act as an NTP server or client. Mostly configured as a client.

### NTP Client

```cisco
clock timezone <timezone>
ntp server x.x.x.1
```

### NTP Server

```cisco
clock timezone <timezone>
ntp master
```

Once set it can take several minutes for the client to drift towards the NTP source.
