# Maintainer: ashwamegh <shashank7200@gmail.com>
# Inspired by macbook-lighter, many thanks to Jun Yang's package https://github.com/harttle/macbook-lighter
pkgname=macbook-lighter-nvidia
pkgver=v0.0.2.5.g112c1f0
pkgrel=1
pkgdesc="Macbook screen/keyboard backlight CLI and auto-adjust on ambient light"
arch=(any)
url="https://github.com/ashwamegh/macbook-lighter-nvidia"
license=('GPL')
depends=('bc')
makedepends=('git')
provides=()
conflicts=()
source=('git+https://github.com/ashwamegh/macbook-lighter-nvidia.git')
md5sums=('SKIP')

pkgver() {
  cd "$srcdir/$pkgname"
  git describe --tags | sed 's|-|.|g'
}

package() {
  cd "$srcdir/$pkgname"
  [ ! -f $pkgdir/etc/macbook-lighter.conf ] && install -Dm644 macbook-lighter.conf $pkgdir/etc/macbook-lighter.conf
  install -Dm644 "macbook-lighter.service" "$pkgdir/usr/lib/systemd/system/macbook-lighter.service"
  install -Dm755 "src/macbook-lighter-ambient.sh" "$pkgdir/usr/bin/macbook-lighter-ambient"
  install -Dm755 "src/macbook-lighter-screen.sh" "$pkgdir/usr/bin/macbook-lighter-screen"
  install -Dm755 "src/macbook-lighter-kbd.sh" "$pkgdir/usr/bin/macbook-lighter-kbd"
}
