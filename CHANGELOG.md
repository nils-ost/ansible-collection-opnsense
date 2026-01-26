# nils_ost opnsense Collection Release Notes

**Topics**

- <a href="#v1-1-0">v1\.1\.0</a>
  - <a href="#release-summary">Release Summary</a>
  - <a href="#minor-changes">Minor Changes</a>
  - <a href="#bugfixes">Bugfixes</a>
- <a href="#v1-0-1">v1\.0\.1</a>
  - <a href="#release-summary-1">Release Summary</a>
- <a href="#v1-0-0">v1\.0\.0</a>
  - <a href="#release-summary-2">Release Summary</a>

<a id="v1-1-0"></a>

## v1\.1\.0

<a id="release-summary"></a>

### Release Summary

With this Release the NAT Outbound rules now work as intended

<a id="minor-changes"></a>

### Minor Changes

- Added <code>enabled</code> attribut to <code>opnsense_nat_outbound</code>\, to be able to disable a filter without deleting it
- Added <code>enabled</code> attribut to <code>opnsense_shaper_pipes</code>\, to be able to disable a pipe without deleting it
- Added <code>enabled</code> attribut to <code>opnsense_shaper_queues</code>\, to be able to disable a queue without deleting it
- Added <code>enabled</code> attribut to <code>opnsense_shaper_rules</code>\, to be able to disable a rule without deleting it
- Changed validation for <code>target</code> attribut of <code>opnsense_nat_outbound</code> to revoke problematic inputs

<a id="bugfixes"></a>

### Bugfixes

- Added reload task\, after NAT outbound filter was added\, changed or purged

<a id="v1-0-1"></a>

## v1\.0\.1

<a id="release-summary-1"></a>

### Release Summary

Just a fast patch after the first release\, to add metadata to \'configure\' role

<a id="v1-0-0"></a>

## v1\.0\.0

<a id="release-summary-2"></a>

### Release Summary

This is the first proper release of <code>nils_ost\.opnsense</code> collection on 2026\-01\-22\.
The added Role was just carried over from an other local project\.
