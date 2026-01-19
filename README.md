# Ansible Collection - nils_ost.opnsense

This repository contains the `nils_ost.opnsense` Ansible Collection.

It came to life as I searched for a way to streamline automated installation and configuration of
[Nginx Proxy Manager](https://nginxproxymanager.com/) instances for different environments, but couldn't find a viable library/collection.
The project started out by develpoing local modules and roles for my playbooks, but as I like to use those on multiple projects it seamed
to be a good idea to outsource everything in a collection.
And thats it: A collection for my purposes but available for everyone who find a need in using it ;)

<!--start requires_ansible-->
<!--end requires_ansible-->

## External requirements

Currently only the `requests` Python library is required by this collection, to be able to run the modules.
As this collection is intended to do it's module call `delegate_to: localhost` it's enough to `pip install requests` locally.

## Included content

<!--start collection content-->
<!--end collection content-->

### Roles

Name | Description
--- | ---
[nils_ost.opnsense.basic_config](https://github.com/nils-ost/ansible-collection-opnsense/blob/main/roles/basic_config/README.md)|configures Nginx Proxy Manager with basic capabilities

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

## Licensing

GNU General Public License v3.0 or later.

See [LICENSE](LICENSE) to see the full text.
