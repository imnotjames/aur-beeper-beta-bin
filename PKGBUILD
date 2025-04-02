# Maintainer: James Ward <james@notjam.es>

## options
: ${_autoupdate:=true}

: ${_system_electron:=true}
: ${_electron_version:=}
: ${_install_path:=opt}

[ -n "${_electron_version}" ] && _system_electron=true
: ${_pkgtype=-beta-bin}

# basic info
_pkgname='beeper'
pkgname="$_pkgname${_pkgtype:-}"
pkgver=4.0.584
pkgrel=1
pkgdesc="A unified messaging app"
url="https://beeper.com/"
license=('LicenseRef-beeper')
arch=('x86_64')
provides=("$_pkgname")
conflicts=("$_pkgname")
source=(
  "${pkgname}-${pkgver}.AppImage"::"https://beeper-desktop.download.beeper.com/builds/Beeper-${pkgver}.AppImage"
  $_pkgname.desktop  
)
sha256sums=(
  'c4a39553502239f0bb408327d04ad40d53958e1a3a726daa5d8d57db3936920b'
  '05832b3a54e5a08cb48cad8f1be598be2fd3d2b538c3b000f698c4704c3c24fd'
)
options=('!strip' '!debug')

build() {
  # extract appimage
  chmod +x "${pkgname}-${pkgver}.AppImage"
  "$srcdir/${pkgname}-${pkgver}.AppImage" --appimage-extract

  # fix apprun script
  sed -Ei \
    's@^(if \[ -z \"\$APPDIR\" ] ; then)$@APPDIR="/'"$_install_path"'/beeper"\n\1@' \
    "$srcdir/squashfs-root/AppRun"
}

package() {
  depends+=('hicolor-icon-theme')

  # desktop file
  install -Dm644 "$srcdir/beeper.desktop" "$pkgdir/usr/share/applications/beeper.desktop"

  # icons
  install -Dm644 "$srcdir/squashfs-root/usr/share/icons/hicolor/0x0/apps/beepertexts.png" \
    "$pkgdir/usr/share/icons/hicolor/0x0/apps/beeper.png"

  # license files
  install -Dm644 "$srcdir/squashfs-root/LICENSE.electron.txt" -t "$pkgdir/usr/share/licenses/$pkgname/"
  install -Dm644 "$srcdir/squashfs-root/LICENSES.chromium.html" -t "$pkgdir/usr/share/licenses/$pkgname/"

  # apprun script
  install -Dm755 "$srcdir/squashfs-root/AppRun" "$pkgdir/usr/bin/beeper"

  # everything else
  install -dm755 "$pkgdir/$_install_path"
  mv "$srcdir/squashfs-root" "$pkgdir/$_install_path/beeper"

  # fix permissions
  chmod -R u+rwX,go+rX,go-w "$pkgdir"
}
