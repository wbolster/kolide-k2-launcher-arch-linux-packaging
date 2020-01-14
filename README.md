Kolide K2 packaging for Arch Linux
==================================

This repository contains packaging scripts to convert a Kolide K2 Launcher Debian (`.deb`) package into a clean [Arch Linux](https://www.archlinux.org/) package.

What?
-----

[Kolide](https://kolide.com/) provides an endpoint security solution based on [osquery](https://www.osquery.io/). The managed [Kolide K2](https://k2.kolide.com/) service integrates with [Slack](https://slack.com/) for easy set-up and also provides a [dashboard](https://k2.kolide.com/) for users.

The Kolide Slack app can give users a download link for a _personalized_ installer package, the [Kolide Launcher](https://kolide.com/launcher/) ([source code](https://github.com/kolide/launcher)). It contains:

- `osquery` itself
- some `osquery` extensions
- an auto-update mechanism
- configuration data
- a personal _secret value_ to enroll a device

Note: **NEVER** share personal packages with others, since it contains a personal secret!

How?
----

- `git clone` this repository

- Download the `.deb` package as a starting point:

  * Open a private chat with the Kolide Slack app
  * Type `installers`
  * Download the `.deb` file

- Move the `.deb` package into the repository worktree.

- Since each package has a unique name, create a symlink so that the packaging script knows which file to use:

  ``` sh
  ln -sf xkxp-*-kolide-launcher.deb kolide-k2-launcher.deb
  ```

- Convert it into a _personal_ Arch package:

  ``` sh
  makepkg --skipinteg
  ```

  Note that skipping the usual file integrity checks is fine here: each `.deb` package is unique and contains a _personal_ secret, so each cryptographic hash will be different.

- Install the resulting package:

  ``` sh
  sudo pacman -U kolide-k2-launcher-*.pkg.tar.zst
  ```

- Yet another reminder: do _not_ share this file with others!

- Check that the `systemd` service is running:

  ``` sh
  systemctl status kolide-k2-launcher.service
  ```

  Note: unlike many other Arch packages, installing this package will automatically enable the `kolide-k2-launcher` systemd unit, which means it will immediately start, and will also automatically start on boot. You're most likely installing this package because of mandatory company policy anyway. This package will make your device compliant with the policy by default.

- Check the log output:

  ``` sh
  journalctl -f -u kolide-k2-launcher.service
  ```

Why?
----

The Linux installers come in Debian/Ubuntu and RPM flavours. There is no official Arch linux package.

While the Debian (`.deb`) packaging for the Kolide K2 Launcher is mostly functional, the package itself is rather sloppy, and the RPM package is no different. Using a tool like [debtap](https://github.com/helixarch/debtap) to convert it into an Arch package will result in an equally sloppy Arch package.

For instance, it pollutes the system with files in non-standard places such as `/usr/local/kolide-k2` (application) and `/var/kolide-k2` (state). This goes against common Linux packaging practices. The [Arch package guidelines](https://wiki.archlinux.org/index.php/Arch_package_guidelines) (and similar guidelines for other Linux distributions) make it very clear that packages should _never_ install into `/usr/local/` and that `/var/lib/{pkg}` is the correct place for persistent application storage.

The packaging scripts do _not_ change the application in any way, but the packaging is a lot more sensible:

- the application is installed into `/opt/kolide-k2/`
- configuration data (including the secret) is stored in `/etc/kolide-k2/`
- state is stored in `/var/lib/kolide-k2/`
- the systemd service is named `kolide-k2-launcher` (instead of `launcher.kolide-k2` which is confusing and ugly)
