# Network Device Management

## Syslog

Syslog is a standard method of logging messages. the format is.

```syslog
<seq number>:<timestamp>:%<facility>-<severity level>-<mnemonic>:<description>
```

The defined fields are.

| Field       | Description                                     |
| ----------- | ----------------------------------------------- |
| seq num     | The sequence number of the alert                |
| timestamp   | The time the event happened                     |
| facility    | Which process or system generated the event log |
| severity    | How urgent is the alert. See the table below    |
| mnemonic    | Short code describing the event log             |
| description | A description of the event log                  |

Alert levels run from 0 through 7 with 0 being the most urgent. The levels are defined as follows.

| Value | Severity      | Description                                                                            |
| ----- | ------------- | -------------------------------------------------------------------------------------- |
|   0   | Emergency     | System is unusable. A panic condition.                                                 |
|   1   | Alert         | A condition that should be corrected immediately, such as a corrupted system database. |
|   2   | Critical      | Critical conditions, such as hard device errors.                                       |
|   3   | Error         | Error conditions.                                                                      |
|   4   | Warning       | Warning conditions.                                                                    |
|   5   | Notice        | Normal but significant conditions.Not errors, but may require special handling.        |
|   6   | Informational | Informational messages.                                                                |
|   7   | Debug         | Messages that contain information normally of use only when debugging a program.       |

Syslog message can be sent to various locations.

- Console - By default Syslog messages show on the console. E.g. serial connection
- VTY Lines - E.g. SSH/Telnet. Messages now show here by default
- Buffer - Events saved in RAM. On by default
- External server - Off by default

Some example commands.

Disable logging to the console.

```cisco
no logging console
```

Show messages of severity 0 through 6.

```cisco
logging monitor 6
```

Log everything to the RAM buffer.

```cisco
logging buffered debugging
```

Send logs to an external Syslog server.

```cisco
logging x.x.x.x
logging trap debugging
```

To show how logging is setup use. This will show the config and the events in the buffer.

```cisco
sh logging
```

To prevent event logs from printing in the middle of commands being typed by using synchronous logging.

```cisco
line con 0
    logging synchronous
    exit
line vty 0 15
    logging synchronous
```

## Debug Commands

Debug commands should be used with care as the amount of information displayed can overwhelm the device. If connecting via ssh/telnet you will need to enable logging to the VTY connection first.

```cisco
terminal monitor
```

You then turn on and off various debugging commands using the `debug` or `undebug` commands respectively. The shortcut to turn off all debugging is `u all`.

## SNMP Simple Network Management Protocol

SNMP is an open standard for network monitoring. A device can be an SNMP Manager which is the server and SNMP Agent like a router. A manager can pull SNMP stats from a device or an agent can send information via a Trap. An SNMP manager can change the state of an agent as well. SNMP data variables are organised into a database called an MID (Management Information Database).

The three versions in common use are.

V1  - Data send in plain text with plain text authentication via community strings
V2c - Also plain text but supports bulk retrieval
V3  - Supports strong encryption and authentication using users and groups

Community strings must match on both agent and server. Community strings can be read only or read write.

### Example SNMPv2c Configuration

```cisco
snmp-server contact <support email>
snmp-server location <describe location>

snmp-server community <community string 1> ro
snmp-server community <community string 2> rw

snmp-server host x.x.x.x <RO community string>
snmp-server enable traps config
```

### Example SNMPv3 Configuration

SNMPv3 uses groups to assign access. Access can be one of these three groups.

- NoAuthNoPriv - No authentication password is used and communications are in plain text. The user name stands in for the community string
- AuthNoPriv   - Password authentication is used. Auth is encrypted but other communications are plain text
- AuthPriv     - A password is used and communications are encrypted

```cisco
snmp-server group <group name> v3 priv
snmp-server user <user name> <group name> auth sha <auth pwd> priv aes [128|192|256] <priv password>
```
