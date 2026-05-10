===========================================
nils\_ost opnsense Collection Release Notes
===========================================

.. contents:: Topics

v1.1.1
======

Release Summary
---------------

loop var for adding DHCP reservations now also includes the name of the reserveration and not just the MAC addr

v1.1.0
======

Release Summary
---------------

With this Release the NAT Outbound rules now work as intended

Minor Changes
-------------

- Added ``enabled`` attribut to ``opnsense_nat_outbound``, to be able to disable a filter without deleting it
- Added ``enabled`` attribut to ``opnsense_shaper_pipes``, to be able to disable a pipe without deleting it
- Added ``enabled`` attribut to ``opnsense_shaper_queues``, to be able to disable a queue without deleting it
- Added ``enabled`` attribut to ``opnsense_shaper_rules``, to be able to disable a rule without deleting it
- Changed validation for ``target`` attribut of ``opnsense_nat_outbound`` to revoke problematic inputs

Bugfixes
--------

- Added reload task, after NAT outbound filter was added, changed or purged

v1.0.1
======

Release Summary
---------------

Just a fast patch after the first release, to add metadata to 'configure' role

v1.0.0
======

Release Summary
---------------

This is the first proper release of ``nils_ost.opnsense`` collection on 2026-01-22.
The added Role was just carried over from an other local project.
