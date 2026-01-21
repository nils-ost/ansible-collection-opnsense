# nils_ost.opnsense.configureS

**configures OPNsense-Services by group or host variables**

Version added: 1.0.0

- [Synopsis](#synopsis)
- [Role Variables](#role-variables)
  - [Structure of: opnsense_vlans](#structure-of-opnsense_vlans)
  - [Structure of: opnsense_gateways](#structure-of-opnsense_gateways)
  - [Structure of: opnsense_dhcp](#structure-of-opnsense_dhcp)
  - [Structure of: opnsense_dhcp_subnets](#structure-of-opnsense_dhcp_subnets)
  - [Structure of: opnsense_dhcp_reservations](#structure-of-opnsense_dhcp_reservations)
  - [Structure of: opnsense_aliases](#structure-of-opnsense_aliases)
  - [Structure of: opnsense_nat_outbound](#structure-of-opnsense_nat_outbound)
  - [Structure of: opnsense_shaper_pipes](#structure-of-opnsense_shaper_pipes)
  - [Structure of: opnsense_shaper_queues](#structure-of-opnsense_shaper_queues)
  - [Structure of: opnsense_shaper_rules](#structure-of-opnsense_shaper_rules)
  - [Structure of: opnsense_rules](#structure-of-opnsense_rules)
- [Pre-Requirements](#pre-requirements)
- [Full usage Example](#full-usage-example)
  - [Playbook](#playbook)
  - [Variables-Definition](#variables-definition)

## Synopsis

Describe what it does and which modules/services are configured

## Role Variables

| Variable                             | Type | Default | Comment                                                                                        |
| ------------------------------------ | ---- | ------- | ---------------------------------------------------------------------------------------------- |
| opnsense_interfaces                  | list | []      | just printed as a helper after VLANs are configured to remember manual interfaces config       |
| opnsense_gateway_groups              | list | []      | just printed as a helper after gateways are configured to remember manual gateway group config |
| opnsense_vlans                       | dict | {}      | holds VLANs to be configured                                                                   |
| opnsense_gateways                    | dict | {}      | holds gateways to be configured                                                                |
| opnsense_dhcp                        | dict | {}      | Basic kea DHCPv4 settings                                                                      |
| opnsense_dhcp_subnets                | dict | {}      | subnet definitions for kea DHCPv4                                                              |
| opnsense_dhcp_reservations           | dict | {}      | ip reservations for kea DHCPv4 clients                                                         |
| opnsense_aliases                     | dict | {}      | firewall aliases to be used in rules, shaper, ...                                              |
| opnsense_nat_outbound                | dict | {}      | custom outbound NAT configuration                                                              |
| opnsense_shaper_pipes                | dict | {}      | pipes definition for firewall shaper                                                           |
| opnsense_shaper_queues               | dict | {}      | queues definition for firewall shaper                                                          |
| opnsense_shaper_rules                | dict | {}      | rules definition for firewall shaper                                                           |
| opnsense_rules                       | dict | {}      |  |
| opnsense_tailscale_advertised_routes | dict | {}      |  |

All used variables (used variables are those which are not empty) are validated at the start of the role. This is done with `ansible.utils.jsonschema` against the schemata found in [criteria](https://github.com/nils-ost/ansible-collection-opnsense/tree/main/roles/configure/criteria).

### Structure of: opnsense_vlans

It's a dict of dicts, where the key of the top-level dictionary defines the description of a VLAN.
The second-level (or value of the top-level dict) sets the variables for this VLAN.

> [!WARNING]
> If this variable is not empty, all VLANs (identified by their description) that are not contained in this dict, are purged when running this role.

The possible variables on second-level are:

| Variable | Type | Required | Comment                                   |
| -------- | ---- | -------- | ----------------------------------------- |
| parent   | str  | true     | name of the parent hw-device              |
| vlan     | int  | true     | vlan-number/-tag (1-4094)                 |
| device   | str  | false    | device-name (needs to start with *vlan0*) |

#### Example

```yaml
opnsense_vlans:
  lan:
    vlan: 1
    parent: "{{ opnsense_hw_interface }}"
  iot:
    vlan: 2
    parent: "{{ opnsense_hw_interface }}"
```

### Structure of: opnsense_gateways

It's a dict of dicts, where the key of the top-level dictionary defines the name of a gateway.
The second-level (or value of the top-level dict) sets the variables for this gateway.

> [!WARNING]
> If this variable is not empty, all gateways (identified by their name) that are not contained in this dict, are purged when running this role.

The possible variables on second-level are:

| Variable        | Type | Required | Comment                                                                              |
| --------------- | ---- | -------- | ------------------------------------------------------------------------------------ |
| desc            | str  | false    | Description                                                                          |
| int             | str  | true     | Interface this gateway applies to                                                    |
| ip              | str  | true     | Address this gateway is reachable at                                                 |
| default         | bool | false    | This will select the above gateway as a default gateway candidate                    |
| far             | bool | false    | This will allow the gateway to exist outside of the interface subnet                 |
| priority        | int  | false    | Influences sort order when selecting a (default) gateway, lower means more important |
| weight          | int  | false    | Weight for this gateway when used in a gateway group                                 |
| monitor         | str  | false    | Alternative address to be used to monitor the link                                   |
| time_period     | int  | false    | The time period over which (monitoring) results are averaged                         |
| force_down      | bool | false    | This will force this gateway to be considered "down"                                 |
| monitor_disable | bool | false    | This will consider this gateway as always being "up"                                 |

#### Example

```yaml
opnsense_gateways:
  GW_DSL:
    int: "{{ opnsense_interface_assignments['dsl'] }}"
    ip: 192.168.0.1
    monitor: 8.8.8.8
    default_gw: true
    priority: 253
  GW_5G:
    int: "{{ opnsense_interface_assignments['5g'] }}"
    ip: 192.168.3.1
    monitor: 8.8.4.4
    default_gw: false
    priority: 254
```

### Structure of: opnsense_dhcp

This dict contains the main setting variables of Kea DHCPv4 service

> [!HINT]
> If this variable is empty, all DHCP tasks are omitted.
> But if it contains at least `enabled` (whether true or false) `dnsmasq` service will be disabled.

The possible variables on second-level are:

| Variable      | Type | Required | Comment                                                               |
| ------------- | ---- | -------- | --------------------------------------------------------------------- |
| enabled       | bool | true     | Whether to enable or disable service                                  |
| interfaces    | list | false    | List of interfaces to listen on                                       |
| auto_fw_rules | bool | false    | Automatically add a basic set of firewall rules to allow dhcp traffic |
| lifetime      | int  | false    | The valid life time of the leases given out by the server in seconds  |

#### Example

```yaml
opnsense_dhcp:
  enabled: true
  interfaces:
    - "{{ opnsense_interface_assignments['lan'] }}"
  auto_fw_rules: true
  lifetime: 3600
```

### Structure of: opnsense_dhcp_subnets

It's a dict of dicts, where the key of the top-level dictionary defines the subnet of a DHCP-subnet.
The second-level (or value of the top-level dict) sets the variables for this DHCP-subnet.

> [!WARNING]
> All DHCP-subnets (identified by their subnet) that are not contained in this dict, are purged when running this role.

The possible variables on second-level are:

| Variable      | Type       | Required | Comment                                                               |
| ------------- | ---------- | -------- | --------------------------------------------------------------------- |
| desc          | str        | false    | Description                                                           |
| pools         | list       | true     | List of pools, in range or subnet format                              |
| gateway       | list\|str  | false    | Default gateways to offer to the clients                              |
| dns           | list\|str  | false    | DNS servers to offer to the clients                                   |
| routes        | list\|str  | false    | Static routes the client should install in its routing cache          |
| domain        | str        | false    | A custom domain name to offer to the clients                          |
| domain_search | list\|str  | false    | 'search list' of Domain Names to be used by client to locate not-fqdn |
| ntp_servers   | list\|str  | false    | List of IP addresses indicating NTP servers available to the client   |
| time_servers  | list\|str  | false    | List of RFC 868 time servers available to the client                  |
| next_server   | str        | false    | Next server IP address                                                |
| tftp_server   | str        | false    | TFTP server address or fqdn                                           |
| tftp_file     | str        | false    | Boot filename to request                                              |

#### Example

```yaml
opnsense_dhcp_subnets:
  192.168.1.0/24:
    desc: lan
    pools:
      - 192.168.1.10-192.168.1.100
    gateway: 192.168.1.1
    dns: 192.168.1.2
```

### Structure of: opnsense_dhcp_reservations

It's a dict of dicts, where the key of the top-level dictionary defines the hw-addr (mac) of a DHCP-reservation.
The second-level (or value of the top-level dict) sets the variables for this DHCP-reservation.

> [!WARNING]
> All DHCP-reservations (identified by their hw-addr) that are not contained in this dict, are purged when running this role.

The possible variables on second-level are:

| Variable | Type | Required | Comment                            |
| -------- | ---- | -------- | ---------------------------------- |
| desc     | str  | false    | Description                        |
| subnet   | str  | false    | Subnet this reservation belongs to |
| ip       | str  | true     | IP address to offer to the client  |
| name     | str  | false    | Offer a hostname to the client     |

#### Example

```yaml
opnsense_dhcp_reservations:
  11:22:33:44:55:66:
    subnet: 192.168.1.0/24
    ip: 192.168.1.11
    name: eggphone
    desc: My cellphone
  77:88:99:aa:bb:cc:
    subnet: 192.168.1.0/24
    ip: 192.168.1.12
    name: universe
    desc: My tablet
```

### Structure of: opnsense_aliases

It's a dict of dicts, where the key of the top-level dictionary defines the name of a alias.
The second-level (or value of the top-level dict) sets the variables for this alias.

> [!WARNING]
> If this variable is not empty, all aliases (identified by their name) that are not contained in this dict, are purged when running this role.

The possible variables on second-level are:

| Variable | Type       | Required | Comment                                                                                               |
| -------- | ---------- | -------- | ----------------------------------------------------------------------------------------------------- |
| desc     | str        | false    | Description                                                                                           |
| type     | str        | true     | One of: host, network, port, url, urltable, geoip, networkgroup, mac, dynipv6host, internal, external |
| content  | list\|str  | true     | List of elements defined by type                                                                      |

#### Example

```yaml
opnsense_aliases:
  workstation:
    type: host
    content: 192.168.1.101
  nas:
    type: host
    content: 192.168.1.9
```

### Structure of: opnsense_nat_outbound

It's a dict of dicts, where the key of the top-level dictionary defines the description of a outbound NAT.
The second-level (or value of the top-level dict) sets the variables for this outbound NAT.

Created rules are found under `Firewall->Automation->Source NAT` **not** at `Firewall->NAT->Outbound`

> [!WARNING]
> If this variable is not empty, all outbound NATs (identified by their description) that are not contained in this dict, are purged when running this role.

The possible variables on second-level are:

| Variable    | Type      | Required | Comment                                                       |
| ----------- | --------- | -------- | ------------------------------------------------------------- |
| seq         | int       | false    | Order in which the rule will be evaluated (lowest first)      |
| int         | str       | false    | Interface                                                     |
| src         | str       | false    | Source                                                        |
| src_port    | int\|str  | false    | Sourece-Port                                                  |
| dst         | str       | false    | Destination                                                   |
| dst_port    | int\|str  | false    | Destination-Port                                              |
| target      | str       | false    | Translation / target matching packets will be mapped this IP  |
| target_port | int       | false    | Translation-Port                                              |

#### Example

```yaml
opnsense_nat_outbound:
  workstation into iot-net:
    int: "{{ opnsense_interface_assignments['lan'] }}"
    src: workstation
    dst: "{{ opnsense_interface_assignments['iot'] }}"
    target: "{{ opnsense_interface_assignments['iot'] }}"
```

### Structure of: opnsense_shaper_pipes

It's a dict of dicts, where the key of the top-level dictionary defines the description of a shaper-pipe.
The second-level (or value of the top-level dict) sets the variables for this shaper-pipe.

> [!WARNING]
> If this variable is not empty, all shaper-pipes (identified by their description) that are not contained in this dict, are purged when running this role.

The possible variables on second-level are:

| Variable  | Type | Required | Comment                                                                                                 |
| --------- | ---- | -------- | ------------------------------------------------------------------------------------------------------- |
| bandwidth | int  | true     | Total bandwidth for this pipe (minimum: 1)                                                              |
| metric    | str  | false    | Bandwidth metric (one of: bit, Kbit, Mbit, Gbit)                                                        |
| mask      | str  | false    | Dynamic pipe creation by src or dst, or none for everything in one pipe (one of: none, src-ip, dst-ip)  |
| queue     | str  | false    | Number of dynamic queues, leave empty for default                                                       |
| buckets   | str  | false    | Specifies the size of the hash table used for storing the various dynamic pipes                         |

#### Example

```yaml
opnsense_shaper_pipes:
  p_forwarding_limit:
    bandwidth: 200
```

### Structure of: opnsense_shaper_queues

It's a dict of dicts, where the key of the top-level dictionary defines the description of a shaper-queue.
The second-level (or value of the top-level dict) sets the variables for this shaper-queue.

> [!WARNING]
> If this variable is not empty, all shaper-queues (identified by their description) that are not contained in this dict, are purged when running this role.

The possible variables on second-level are:

| Variable | Type | Required | Comment                                                                                                  |
| -------- | ---- | -------- | -------------------------------------------------------------------------------------------------------- |
| pipe     | str  | true     | Connected pipe for this queue                                                                            |
| weight   | int  | false    | Weight of this queue (1..100), used to prioritize within a pipe. (1 is low, 100 is high)                 |
| mask     | str  | false    | Dynamic queue creation by src or dst, or none for everything in one queue (one of: none, src-ip, dst-ip) |

#### Example

```yaml
opnsense_shaper_queues:
  q_lan:
    weight: 30
    pipe: p_router_max
  q_iot:
    weight: 20
    pipe: p_router_max
```

### Structure of: opnsense_shaper_rules

It's a dict of dicts, where the key of the top-level dictionary defines the description of a shaper-rule.
The second-level (or value of the top-level dict) sets the variables for this shaper-rule.

> [!WARNING]
> If this variable is not empty, all shaper-rules (identified by their description) that are not contained in this dict, are purged when running this role.

The possible variables on second-level are:

| Variable | Type      | Required | Comment                                                                                                                |
| -------- | --------- | -------- | ---------------------------------------------------------------------------------------------------------------------- |
| pipe     | str       | false    | Target pipe (exactly one of pipe or queue is required)                                                                 |
| queue    | str       | false    | Target queue (exactly one of pipe or queue is required)                                                                |
| seq      | int       | false    | Order in which the rule will be evaluated (lowest first)                                                               |
| int      | str       | false    | Primary interface. Can be combined with direction                                                                      |
| int2     | str       | false    | Secondary interface, matches packets traveling to/from interface 1 to/from interface 2. Can be combined with direction |
| src      | list\|str | false    | Source ip(s) or network(s)                                                                                             |
| src_port | int\|str  | false    | Source port (range)                                                                                                    |
| dst      | list\|str | false    | Destination ip(s) or network(s)                                                                                        |
| dst_port | int\|str  | false    | Destination port (range)                                                                                               |
| dir      | str       | false    | Matches incoming (in) or outgoing (out) packets or both (if left empty)                                                |

#### Example

```yaml
opnsense_shaper_rules:
  r_lan:
    seq: 1
    int: "{{ opnsense_interface_assignments['lan'] }}"
    queue: q_lan
  r_iot:
    seq: 2
    int: "{{ opnsense_interface_assignments['iot'] }}"
    queue: q_iot
```

### Structure of: opnsense_rules

It's a dict of dicts, where the key of the top-level dictionary defines the description of a rule.
The second-level (or value of the top-level dict) sets the variables for this rule.

Created rules are found under `Firewall->Automation->Filter` **not** at `Firewall->Rules`

> [!WARNING]
> If this variable is not empty, all rules (identified by their description) that are not contained in this dict, are purged when running this role.

The possible variables on second-level are:

| Variable    | Type      | Required | Comment                                                                                                                             |
| ----------- | --------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| action      | str       | false    | Choose what to do with packets that match this rule (one of: pass, block, reject)                                                   |
| int         | list\|str | true     | Interface(s) this rule applies to                                                                                                   |
| dir         | str       | false    | Direction of the traffic. The default (in) sets the policy to the interface originally receiving the traffic. The opposite is out   |
| ip_proto    | str       | false    | IP version v4, v6 or both (one of: inet, inet6, inet46)                                                                             |
| proto       | str       | false    | Protocol to match (such as: TCP, UDP, ICMP, ...)                                                                                    |
| src_invert  | bool      | false    | Use this option to invert the sense of the source match                                                                             |
| src         | list\|str | false    | Source ip(s) or network(s)                                                                                                          |
| src_port    | int\|str  | false    | Source port (range)                                                                                                                 |
| dst_invert  | bool      | false    | Use this option to invert the sense of the destination match                                                                        |
| dst         | list\|str | false    | Dstination ip(s) or network(s)                                                                                                      |
| dst_port    | int\|str  | false    | Destination port (range)                                                                                                            |
| seq         | int       | false    | Order in which the rule will be evaluated (lowest first)                                                                            |
| quick       | bool      | false    | If a packet matches a rule specifying quick, then that rule is considered the last matching rule and the specified action is taken. |
| log         | bool      | false    | Log packets that are handled by this rule                                                                                           |
| gw          | str       | false    | Leave empty to use the system routing table. Or choose a gateway to utilize policy based routing.                                   |
| enabled     | bool      | false    | Enable this rule                                                                                                                    |

#### Example

```yaml
opnsense_rules:
  block a host:
    seq: 1
    int: "{{ opnsense_interface_assignments['lan'] }}"
    src: "192.168.1.47"
    action: "reject"
    enabled: true
  workstation is allowed to enter iot net:
    seq: 2
    int: "{{ opnsense_interface_assignments['lan'] }}"
    src: workstation
    dst: "{{ opnsense_interface_assignments['iot'] }}"
    action: "pass"
    enabled: true
  define default gateway (group) for lan:
    seq: 3
    int: "{{ opnsense_interface_assignments['lan'] }}"
    src: "{{ opnsense_interface_assignments['lan'] }}"
    action: "pass"
    gw: GWG_failover
    enabled: true
```

## Pre-Requirements

install, local-access, https and api-key

## Full usage Example

### Playbook

### Variables-Definition
