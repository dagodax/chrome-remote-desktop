pkgname=chrome-remote-desktop
pkgver=47.0.2526.18
pkgrel=1
pkgdesc="Allows you to securely access your computer over the Internet through Chrome."
url="https://chrome.google.com/webstore/detail/gbchcmhmhahfdphkhkmpfmihenigjmpp"
arch=('x86_64')
license=('BSD')
install=$pkgname.install
depends=('python2' 'python2-psutil' 'gconf' 'gtk2' 'nss' 'xorg-utils' 'xorg-server' 'xorg-xkb-utils' 
         'xorg-xauth' 'nano')       
source=("https://dl.google.com/linux/direct/${pkgname}_current_amd64.deb"
	"$pkgname.service"
        "crd")
md5sums=('SKIP' 
         '6f6083ff37f036f590702c7b1319445b'
         '5e9fa07e85d0d490de675bf258a0c511')

pkgver() {
  bsdtar -xf control.tar.gz -O control | grep '^Version: ' | cut -f2 -d' '
}

prepare() {
  cd "$srcdir"

  msg2 'Extracting data from debian package'
  bsdtar -xf data.tar.gz -C .

  msg2 'Patching Python script'
  sed -e '1 s/python/python2/' \
      -e '/^.*sudo_command =/ s/"gksudo .*"/"pkexec"/' \
      -e '/^.*command =/ s/s -- sh -c/s sh -c/' \
      -i opt/google/chrome-remote-desktop/chrome-remote-desktop
}

build() {
  cd "$srcdir"

  msg2 'Removing unnecessary files'
  rm -R etc/cron.daily
  rm -R etc/init.d
  rm -R etc/pam.d
}

package() {
  cd "$srcdir"

  mv etc $pkgdir
  mv opt $pkgdir

  msg2 'Packaging copyright file'
  install -Dm644 usr/share/doc/$pkgname/copyright "$pkgdir/usr/share/licenses/$pkgname/copyright"

  msg2 "Adding systemd user service file"
  install -Dm644 "$pkgname.service" "$pkgdir/usr/lib/systemd/user/$pkgname.service"
  
  msg2 "Adding runnable crd command"
  install -Dm755 "crd" "$pkgdir/usr/bin/crd"

  msg2 "Creating symlinks for chromium compatibility"
  install -dm755 "$pkgdir/etc/chromium/native-messaging-hosts"
  for _file in $( find "$pkgdir/etc/opt/chrome/native-messaging-hosts" -type f); do
    _filename=${_file##*/}
    if [[ ! -f "/etc/chromium/native-messaging-hosts/${_filename}" ]]; then
      ln -s "/etc/opt/chrome/native-messaging-hosts/${_filename}" \
	    "$pkgdir/etc/chromium/native-messaging-hosts/${_filename}"
    fi
  done
}
