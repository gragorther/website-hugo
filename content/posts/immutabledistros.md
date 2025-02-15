---
title: Immutable Linux Distributions
date: "2024-08-05"
categories:
  - linux
  - tech
---

# What are immutable distros?

In immutable distros, you can't modify the root partition of your operating system, which provides protection against accidentally bricking your operating system. They also have the ability to do rollbacks, so that if an update breaks the operating system or if you break it, you can just select the previous system image in the GRUB menu, because that's the way it handles updates. It creates a new system image for every update. I actually used Fedora Kinoite, which is like Fedora Silverblue, but with KDE instead of GNOME. I am currently using BlendOS.

# The shortcomings of immutable distros

Immutable distributions have a lot of awesome capabilites, but that's not to say they are perfect. If you like to tinker a lot with your operating system, immutable distros are not for you. It's not easy to modify files in the root partition and you have to restart your computer for every package installed via rpm-ostree (the tool Fedora's immutable distros use) or its equivalent in other immutable operating systems. I recently managed to break my Fedora Kinoite install by messing up the permissions system. I distrohopped to BlendOS (I hope I don't break it this time)

# The distros I tested

I tested OpenSUSE Aeon, Fedora Kinoite (which was my daily driver), BlendOS (to which I switched a few days ago) and VanillaOS.

The VanillaOS installer refused to boot in my case, showing some amdgpu error, so there isn't much to discuss here. VanillaOS is based on Debian Sid, which is an experimental branch of Debian, so the error might be due to experimental software.

VanillaOS distributes security updates as Vib modules, you can learn more about them at [https://vib.vanillaos.org/](https://vib.vanillaos.org/). Everything in VanillaOS is a container, even the default shell opens a Debian container.

Aeon is very similar to Fedora Kinoite, with the difference that it uses GNOME instead of KDE. It has distrobox preinstalled, so that you can install apps that aren't packaged with flatpak or are packaged for other distributions on Aeon. The installer for Aeon is not the best looking. If you'd like to install Kalpa, which is Aeon's KDE spin, I would not recommend it. The installer is very out of date, still using the one from MicroOS (from which Aeon and Kalpa were split). I couldn't even select the disk to which I wish to install to. Though this is all understandable, as Kalpa is still in alpha. I do not recommend using either of them, because they are not as feature complete as Fedora Silverblue and BlendOS.

Fedora Kinoite is a KDE spin of Fedora Silverblue. It uses rpm-ostree for the system package management, through which you can install packages on the system, not in a container or Flatpak, however this is not recommended, because the system should contain only the most important packages. Everything else should be either in a container or Flatpak, except for packages which absolutely need to be on the system level, such as VPN applications or Virtualbox kernel components..

BlendOS is quite similar to VanillaOS, with the difference that it is based on Arch. It has the ability to run apps in containers, though this can be added to any other distro with [distrobox](https://distrobox.it/). BlendOS even has a graphical interface for them and allows you to run apps with a GUI, not just CLI apps. It has a config file located at /system.yaml, in which you can configure everything for the next system image build, such as which desktop environment it uses, which Arch packages it should install the usual way, ie. not in a container, the repository it uses, the commands it runs at startup, basically all configuration for an OS. BlendOS doesn't offer rollbacks, which is the main point of immutable distros, so I find that quite silly. It is developed mostly by Rudra Saraswat, a 14-year-old, which is...impressive.

# Conclusion

Immutable distros have their perks, but there is also an alternative to it: btrfs snapper. It creates occasional snapshots of the filesystem, allowing you to revert at any point to the previous filesystem. It provides the same benefits of an immutable distro, but without the immutability, allowing you to install apps the normal way.
