pkgname=kolide-k2-launcher
pkgver=0.10.3
pkgrel=1
pkgdesc="Kolide Launcher for Osquery"
arch=('x86_64')
url="https://kolide.com/launcher/"
license=('MIT')
install="${pkgname}.install"
depends=("glibc>=2.28")
optdepends=()
makedepends=()
source=("${pkgname}.deb" "${pkgname}.patch")
noextract=("${pkgname}.deb")

package() {
  bsdtar -Oxf "${pkgname}.deb" data.tar.gz | bsdtar -C "${pkgdir}" -xzf -

  install -d "${pkgdir}/opt"
  mv -t "${pkgdir}/opt/" "${pkgdir}/usr/local/kolide-k2"

  install -d "${pkgdir}/var/lib"
  mv -t "${pkgdir}/var/lib/" "${pkgdir}/var/kolide-k2"

  install -d "${pkgdir}/usr/lib/systemd/system"
  mv "${pkgdir}/lib/systemd/system/launcher.kolide-k2.service" \
     "${pkgdir}/usr/lib/systemd/system/kolide-k2-launcher.service"
  find "${pkgdir}/lib/" -type d -empty -delete

  rm "${pkgdir}/usr/share/doc/launcher-kolide-k2/changelog.gz"
  find "${pkgdir}/usr/" -type d -empty -delete

  find "${pkgdir}" -type d -exec chmod 755 {} +
  chmod 700 "${pkgdir}/etc/kolide-k2/secret"

  cd "${pkgdir}"
  patch --forward --strip=2 --input="${srcdir}/${pkgname}.patch"
}

pkgver() {
  bsdtar -Oxf "${pkgname}.deb" control.tar.gz \
  | bsdtar -Oxzf - ./control \
  | grep ^Version \
  | cut -d' ' -f2
}
