# Maintainer: Your Name <you@example.com>

pkgname=yourproject
pkgver=0.1.0
pkgrel=1
pkgdesc="Short one-line description"
arch=('any')
url="https://github.com/you/yourproject"
license=('MIT')
depends=()

# build to /tmp to keep the repo clean
PKGDEST=/tmp

package() {
  install -Dm755 "$startdir/yourproject" "$pkgdir/usr/bin/yourproject"
}
