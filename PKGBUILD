pkgname=switch-libpython
pkgver=3.8.0a3
pkgrel=1
pkgdesc="The Python programming language (Nintendo Switch port)"
arch=('any')
url="https://www.python.org/"
license=("custom")
options=(!strip libtool staticlibs)
makedepends=('switch-pkg-config' 'devkitpro-pkgbuild-helpers')
depends=('libnx')
source=("git+https://github.com/DavidBuchanan314/cpython.git")
md5sums=('SKIP')
groups=('switch-portlibs')

build() {
  cd cpython

  cp ../../Setup Modules/Setup

  source /opt/devkitpro/switchvars.sh

  ./configure \
    LDFLAGS="-specs=$DEVKITPRO/libnx/switch.specs $LDFLAGS" \
    CONFIG_SITE="../../config.site" \
    --host=aarch64-none-elf \
    --build=$(./config.guess) \
    --prefix="$PORTLIBS_PREFIX" \
    --disable-ipv6 \
    --disable-shared

  # configure gets a few things wrong due to misleading newlib headers
  # I can't see any other way to override it...
  for func in SETGROUPS FCHDIR FDATASYNC SYMLINK CHROOT
  do
    sed -i "s/#define HAVE_$func 1/\/* #undef HAVE_$func *\//" pyconfig.h
  done

  make
}

package() {
  cd cpython

  source /opt/devkitpro/switchvars.sh

  # for some reason libainstall will fail unless we also do altbininstall
  make DESTDIR="$pkgdir" altbininstall libinstall inclinstall libainstall

  # remove the python binaries (we only care about libpython)
  rm -r $pkgdir$PORTLIBS_PREFIX/bin

  # remove the test module (it's big)
  rm -r $pkgdir$PORTLIBS_PREFIX/lib/python*/test
}

