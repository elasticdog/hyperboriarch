# Maintainer: Aaron Bull Schaefer <aaron@elasticdog.com>
# Contributor: Prurigro
# Contributor: Werecat
# Contributor: Xyne

pkgname=cjdns-git
_gitname=cjdns
pkgver=0.0
pkgrel=1
pkgdesc='A routing engine designed for security, scalability, speed and ease of use.'
arch=('i686' 'x86_64' 'armv6h')
url="https://github.com/cjdelisle/${_gitname}"
license=('GPL3')
makedepends=('git' 'nodejs')
optdepends=('libnacl: speed up the build process by skipping the need to compile cnacl')
provides=("${_gitname}")
conflicts=("${_gitname}")
options=('!buildflags' '!makeflags')
source=("git://github.com/cjdelisle/${_gitname}.git#branch=master")
sha256sums=('SKIP')

pkgver() {
  cd "${_gitname}"
  printf '0.%s.%s' "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
  if [[ ! -L "${srcdir}/pybin/python" ]]; then
    install -d "${srcdir}/pybin"
    ln -s /usr/bin/python2 "${srcdir}/pybin/python"
  fi
}

build() {
  cd "${srcdir}/${_gitname}"
  PATH="${srcdir}/pybin:$PATH" NO_DEBUG=1 ./do
}

package() {
  cd "${srcdir}/${_gitname}"
  install -D -m755 cjdroute "${pkgdir}/usr/bin/cjdroute"
  install -D -m644 "contrib/systemd/${_gitname}.service" "${pkgdir}/usr/lib/systemd/system/${_gitname}.service"
}
