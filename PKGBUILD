# $Id$
# Maintainer: Ng Oon-Ee <ngoonee.talk@gmail.com
# Based off virtualbox-modules package, listed maintainers at the time below
# Maintainer: Ionut Biru <ibiru@archlinux.org>
# Maintainer: S�bastien Luttringer <seblu@aur.archlinux.org>

pkgname=virtualbox-host-modules-all
pkgdesc='Host kernel modules for VirtualBox'
pkgver=4.3.10
pkgrel=1
arch=('i686' 'x86_64')
url='http://virtualbox.org'
license=('GPL')
depends=('virtualbox-host-modules' "virtualbox-host-dkms=${pkgver}")
install=virtualbox-host-modules-all.install
makedepends=('linux-headers')

USE_PACMAN_VERSION=0

build() {
  # dkms need modification to be run as user
  cp -r /var/lib/dkms .
  echo "dkms_tree='$srcdir/dkms'" > dkms.conf

if [ "$USE_PACMAN_VERSION" = "0" ]; then
  _KERNELS=`file -rk /boot/* | grep 'Linux kernel.*boot executable' | sed 's/.*version \([^ ]\+\).*/\1/' | grep -v 'ARCH'`
else
  _PACKAGES=`pacman -Qsq linux`
  _KERNELS=`pacman -Ql $_PACKAGES | grep /modules.alias.bin | sed 's/.*\/lib\/modules\/\(.*\)\/modules.alias.bin/\1/g' | grep -v 'ARCH'`
fi

# Loop through all detected kernels
for _kernver in $_KERNELS;
do  
  # build host modules
  msg2 "Host modules for ${_kernver}"
  dkms --dkmsframework dkms.conf build "vboxhost/$pkgver" -k "$_kernver"
done
}

package() {
if [ "$USE_PACMAN_VERSION" = "0" ]; then
  _KERNELS=`file -rk /boot/* | grep 'Linux kernel.*boot executable' | sed 's/.*version \([^ ]\+\).*/\1/' | grep -v 'ARCH'`
else
  _PACKAGES=`pacman -Qsq linux`
  _KERNELS=`pacman -Ql $_PACKAGES | grep /modules.alias.bin | sed 's/.*\/lib\/modules\/\(.*\)\/modules.alias.bin/\1/g' | grep -v 'ARCH'`
fi

# Loop through all detected kernels
for _kernver in $_KERNELS;
do  
  _extraver=extramodules-${_kernver%.*}-${_kernver##*[0-9]-}

  install -dm755 "$pkgdir/usr/lib/modules/$_extraver"
  cd "$srcdir/dkms/vboxhost/$pkgver/$_kernver/$CARCH/module"
  install -m644 * "$pkgdir/usr/lib/modules/$_extraver"
done
  find "$pkgdir" -name '*.ko' -exec gzip -9 {} +
}
sha512sums=()
