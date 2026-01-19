# nils_ost.opnsense.configureS

**configures OPNsense-Services by group or host variables**

Version added: 1.0.0

- [Synopsis](#synopsis)
- [Role Variables](#role-variables)
  - [Structure of: opnsense_vlans](#structure-of-opnsense_vlans)
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
| opnsense_vlans                       | dict | {}      |  |
| opnsense_gateways                    | dict | {}      |  |
| opnsense_dhcp                        | dict | {}      |  |
| opnsense_dhcp_subnets                | dict | {}      |  |
| opnsense_dhcp_reservations           | dict | {}      |  |
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

## Pre-Requirements

install, local-access, https and api-key

## Full usage Example

### Playbook

### Variables-Definition
