# Maintainer: Aaron Bull Schaefer <aaron@elasticdog.com>
# Contributor: Enric Morales <geekingaround@enric.me>

pkgname=cjdcmd-git
_gitname=cjdcmd
pkgver=0.0
pkgrel=1
pkgdesc='a command line tool for interacting with cjdns'
arch=('i686' 'x86_64' 'armv6h')
url="https://github.com/inhies/${_gitname}"
license=('GPL3')
makedepends=('mercurial' 'git' 'go')
provides=("${_gitname}")
conflicts=("${_gitname}")
source=("git://github.com/inhies/${_gitname}.git#branch=master")
sha256sums=('SKIP')
_gourl=github.com/inhies/${_gitname}

pkgver() {
  cd "${_gitname}"
  printf '0.%s.%s' "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
  cd "${srcdir}"

  GOPATH="${srcdir}" go get -fix -v -x ${_gourl}
}

package() {
  cd "${srcdir}"

  install -D -m755 "bin/${_gitname}" "${pkgdir}/usr/bin/${_gitname}"
}
