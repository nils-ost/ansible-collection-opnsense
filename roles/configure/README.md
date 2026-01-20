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
| opnsense_aliases                     | dict | {}      |  |
| opnsense_nat_outbound                | dict | {}      |  |
| opnsense_shaper_pipes                | dict | {}      |  |
| opnsense_shaper_queues               | dict | {}      |  |
| opnsense_shaper_rules                | dict | {}      |  |
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

## Pre-Requirements

install, local-access, https and api-key

## Full usage Example

### Playbook

### Variables-Definition
