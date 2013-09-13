# Maintainer: Aaron Bull Schaefer <aaron@elasticdog.com>
# Contributor: Prurigro
# Contributor: Werecat
# Contributor: Xyne

pkgname=cjdns-git
_gitname=cjdns
pkgver=0.0
pkgrel=1
pkgdesc='A routing engine designed for security, scalability, speed and ease of use.'
arch=('i686' 'x86_64')
url="https://github.com/cjdelisle/${_gitname}"
license=('GPL3')
makedepends=('cmake' 'git')
optdepends=('libnacl: speed up the build process by skipping the need to compile cnacl')
provides=("${_gitname}")
conflicts=("${_gitname}")
source=("git://github.com/cjdelisle/${_gitname}.git#branch=master")
sha256sums=('SKIP')

pkgver() {
  cd "${_gitname}"
  printf '%s.%s' "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
  cd "${srcdir}/${_gitname}"

  # disable makepkg optimizations; use upstream defaults
  unset CFLAGS
  unset CPPFLAGS
  unset MAKEFLAGS

  NO_DEBUG=1 ./do
}

package() {
  cd "${srcdir}/${_gitname}"

  install -D -m755 "build/admin/angel/${_gitname}" "${pkgdir}/usr/bin/${_gitname}"
  install -D -m755 build/admin/angel/cjdroute2 "${pkgdir}/usr/bin/cjdroute"
  install -D -m755 contrib/bash/i_am_stupid.sh "${pkgdir}/usr/bin/${_gitname}-recoverconfig"
  install -D -m644 "contrib/systemd/${_gitname}.service" "${pkgdir}/usr/lib/systemd/system/${_gitname}.service"
}
