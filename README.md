## Tumbleweed Repository for XLibre
This repository provides RPM packages and source RPMs
for XLibre (a fork of the X.Org X11 server) on openSUSE Tumbleweed.

## Adding the Repositories
Download the repository configuration files 
from the repo root and place them in /etc/zypp/repos.d/

```sh
sudo cp *.repo /etc/zypp/repos.d/
sudo zypper refresh
```

If you need to enable/disable the src repo first do 
```sh
sudo zypper lr
```
Make note of the number of the "xlibre-git-srcs" repo. 
Normally you would keep this repo disabled 
Take notice of the number of the src repo

To enable the repo do:
```sh
sudo zypper mr -e <number>
```

To disable the repo do:
```sh
sudo zypper mr -d <number>
```

## Install the XLibre Server
Check to see that xf86-input-libinput is installed or 
you won't have a keyboard and mouse in 
graphical runlevel 5 - runlevel 3 should work regardless.
If you get a conflict chooser question choice number 3 or 4 "ignore dependencies" is the correct choice
```sh
sudo zypper in  x11-xlibre
```
If you compile your video (or input) drivers "the hard way"
(Nvidia.*.run) like I do be sure to install the x11-xlibre-devel
package. Check with "zypper se x11-xlibre-devel" to see if they are installed already

## See what's installed 
```sh
sudo zypper se xlibre
```

## Install the rest (optional) 
```sh
sudo zypper install x11-xlibre-meta
```

#### After this step log out and back in (or reboot if necessary)

## Building from Source
If you want to compile from sources, there is a file named 
compile_xlibre_libinput in the xlibre-sources directory with similar instructions.

Note: The pre-built RPMs do not include XNEST.

If you need XNEST support, remove -Dxnest=false from the meson command line.

## 1. Install build dependencies

Install necessary build packages
```sh
zypper in gcc cmake meson xorg-x11-server-source xorg-x11-server-Xspice \
libX11-devel libpixman-1-0-devel libXfont2-devel libxshmfence-devel \
xkbcomp-devel dbus-1-devel libseat1 systemd-devel font-util \
libxcvt-devel seatd-devel libgbm-devel libepoxy-devel nettle \
libsha1detectcoll-devel libgcrypt-devel libopenssl-3-devel libnettle-devel \
libmd-devel xcb xcb-proto-devel xcb-util-devel xcb-util-wm-devel \
libpciaccess-devel libdrm-devel libinput-devel xf86-input-libinput-devel \
xorgproto-devel util-macros-devel libXdmcp-devel libXres-devel spice-protocol-devel
```

Extract the XLibre source tarball and cd into the extracted directory
```sh
export XLIBRE_SRC="$(pwd)"
export XLIBRE_BUILD="${XLIBRE_SRC}/build"
export XLIBRE_PREFIX="/usr" 

meson setup --prefix "$XLIBRE_PREFIX" "$XLIBRE_BUILD"  \
                        --buildtype release -Dxnest=false \
                        --localstatedir /var \
                        --sysconfdir /etc/X11

ninja -C "$XLIBRE_BUILD" install
```

Important note: The --localstatedir=/var option is required.

If you use the default /usr/local/var, XLibre will fail to start.

At a minimum you must also install libinput or you will
have no keyboard and mouse runlevel 5 (Graphical)

## Build and install libinput 
Extract the libinput source tarball and cd into the extracted directory
```sh
PKG_CONFIG_PATH="${XLIBRE_PREFIX}/lib/pkgconfig"
ninja -C build install
```
You're done !! Reboot


 
