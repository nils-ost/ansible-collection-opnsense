# Ansible Collection - nils_ost.opnsense

This repository contains the `nils_ost.opnsense` Ansible Collection.

I like using [OPNsense](https://opnsense.org/) as a router for my different environments.
Usually I use the same set of functions/services and don't like to write a custom playbook everytime.
Therefore this collection (or more specific the role within) contains all configurations I've ever used, parameterized by structured variables.
Now I just have to define the variables of services I like to configure, use the role `nils_ost.opnsense.configure`.
And thats it: A collection for my purposes but available for everyone who find a need in using it ;)

<!--start requires_ansible-->
<!--end requires_ansible-->

## External requirements

  * python:
    * httpx *>=0.28.1*
  * collections:
    * community.general *>=12.2.0*
    * oxlorg.opnsense *>=25.7.8*

```
pip install httpx
ansible-galaxy collection install community.general --upgrade
ansible-galaxy collection install oxlorg.opnsense
```

## Included content

<!--start collection content-->
<!--end collection content-->

### Roles

Name | Description
--- | ---
[nils_ost.opnsense.configure](https://github.com/nils-ost/ansible-collection-opnsense/blob/main/roles/configure/README.md)|configures OPNsense-Services by group or host variables

## Using this collection

```bash
ansible-galaxy collection install nils_ost.opnsense
```

You can also include it in a `requirements.yml` file and install it via
`ansible-galaxy collection install -r requirements.yml` using the format:

```yaml
collections:
  - name: nils_ost.opnsense
```

To upgrade the collection to the latest available version, run the following
command:

```bash
ansible-galaxy collection install nils_ost.opnsense --upgrade
```

You can also install a specific version of the collection, for example, if you
need to downgrade when something is broken in the latest version (please report
an issue in this repository). Use the following syntax where `X.Y.Z` can be any
[available version](https://galaxy.ansible.com/nils_ost/opnsense):

```bash
ansible-galaxy collection install nils_ost.opnsense:==X.Y.Z
```

See
[Ansible Using Collections](https://docs.ansible.com/ansible/latest/user_guide/collections_using.html)
for more details.

## Release notes

See the
[changelog](CHANGELOG.md).

## Roadmap

This collection is mainly intended to be used by myself. Therefor I'm just developing the stuff I need for my current projects on a irregular basis.
But if you find some benefit in this collection, feel free to use it. If you like to have some features added feel free to create a pull-request
or write an issue with a feature-request and I'm going to see if I can make it happen.

Currently I imagine a more guided setup of tailscale, as this is currently quite rudimentary. If this bothers me enough I'm going to tackle it...

## Licensing

GNU General Public License v3.0 or later.

See [LICENSE](LICENSE) to see the full text.
