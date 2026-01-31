## Tumbleweed Repository for XLibre

This repository provides RPM packages and source RPMs for XLibre (a fork of the X.Org X11 server) on openSUSE Tumbleweed.

### Adding the Repositories with zypper

```sh
# Main binary packages repository (x86_64) - enabled by default
sudo zypper addrepo \
  --name "xlibre-git-x86_64" \
  --enable \
  --refresh \
  --gpgcheck \
  https://raw.githubusercontent.com/xlibre-opensuse/repo/main/x86_64/ \
  xlibre-git-x86_64
  
# Source RPMs repository - disabled by default
# Enable it only when you need to build from source:
#   sudo zypper modifyrepo --enable xlibre-git-srcs
sudo zypper addrepo \
  --name "xlibre-git-srcs" \
  --disable \
  --refresh \
  --gpgcheck \
  https://raw.githubusercontent.com/xlibre-opensuse/repo/main/SRPMS/ \
  xlibre-git-srcs
  
  sudo zypper refresh
  
# If you need to enable the src repo first do 
zypper lr

# Make note of the number of the "xlibre-git-srcs" repo then 
zypper mr -e <number>
# To disable the repo again do
zypper mr -d <number>

```
# Building from Source
If you want to compile from sources, there is a file named compile_xlibre_libinput in the xlibre-sources directory with similar instructions.

Note: The pre-built RPMs do not include XNEST.
If you need XNEST support, remove -Dxnest=false from the meson command line.

# 1. Install build dependencies
```sh
# Install necessary build packages
zypper in gcc cmake meson xorg-x11-server-source xorg-x11-server-Xspice \
libX11-devel libpixman-1-0-devel libXfont2-devel libxshmfence-devel \
xkbcomp-devel dbus-1-devel libseat1 systemd-devel font-util \
libxcvt-devel seatd-devel libgbm-devel libepoxy-devel nettle \
libsha1detectcoll-devel libgcrypt-devel libopenssl-3-devel libnettle-devel \
libmd-devel xcb xcb-proto-devel xcb-util-devel xcb-util-wm-devel \
libpciaccess-devel libdrm-devel libinput-devel xf86-input-libinput-devel \
xorgproto-devel util-macros-devel libXdmcp-devel libXres-devel spice-protocol-devel

# Extract the XLibre source tarball and cd into the extracted directory
export XLIBRE_SRC="$(pwd)"
export XLIBRE_BUILD="${XLIBRE_SRC}/build"
export XLIBRE_PREFIX="/usr"           # Recommended: system-wide install in /usr

meson setup --prefix "$XLIBRE_PREFIX" "$XLIBRE_BUILD" --buildtype release -Dxnest=false --localstatedir /var --sysconfdir /etc/X11

ninja -C "$XLIBRE_BUILD" install

# Important note: The --localstatedir=/var option is required.
# If you use the default /usr/local/var, XLibre will fail to start.
# At a minimum you must also install libinput or you will
# have no keyboard and mouse runlevel 5 (Graphical)

# 3. Build and install libinput 
# Extract the libinput source tarball and cd into the extracted directory
PKG_CONFIG_PATH="$$   {XLIBRE_PREFIX}/lib/pkgconfig:   $${PKG_CONFIG_PATH}"
ninja -C build install

# You're done !! Reboot
 
 
