
Debian
====================
This directory contains files used to package radiusd/radius-qt
for Debian-based Linux systems. If you compile radiusd/radius-qt yourself, there are some useful files here.

## radius: URI support ##


radius-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install radius-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your radius-qt binary to `/usr/bin`
and the `../../share/pixmaps/radius128.png` to `/usr/share/pixmaps`

radius-qt.protocol (KDE)

