
 `$Id$`

This document describes the steps required in order to prevent _TeamViewer_
[packaged](https://www.teamviewer.com/en/download/linux/)
for _Debian Linux_ from silently auto-updating itself.

It is assumed that all commands are run as `root`.

1. Add the necessary diversions (will affect package files once those are installed):
   ```bash
   mkdir -p /etc/apt/sources.list.d.disabled
   dpkg-divert --local --rename --divert /etc/apt/sources.list.d.disabled/teamviewer.list /etc/apt/sources.list.d/teamviewer.list
   ```
   There's an alternative method to prevent packages from installing new sources
   under  `/etc/apt/sources.list.d`: one needs to add a file to
   `/etc/dpkg/dpkg.cfg.d/` (named, let's say, `no-new-sources`), with the
   following contents:
   ```
   #
   # /etc/dpkg/dpkg.cfg.d/no-new-sources
   #
   # vim:ft=conf:
   #
   # Prevent packages from installing new sources under /etc/apt/sources.list.d
   #

   path-exclude=/etc/apt/sources.list.d/*
   ```
1. Install the package as usual:
   ```bash
   dpkg -i teamviewer_13.2.26559_amd64.deb
   ```
1. _Apt_ may still have _TeamViewer_'s GPG key present in its trusted database
   (`/etc/apt/trusted.gpg`):
   ```
   $ apt-key list
   ...
   pub   rsa4096 2017-03-13 [SC]
         8CAE 012E BFAC 38B1 7A93  7CD8 C5E2 2450 0C12 89C0
   uid           [ unknown] TeamViewer GmbH (TeamViewer Linux 2017) <support@teamviewer.com>
   sub   rsa4096 2017-03-13 [E]
   ...
   ```
   If this is the case, delete the key:
   ```bash
   apt-key del 0C1289C0
   ```
1. That's it.
