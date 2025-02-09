# Maintainer: asm0dey <pavel.finkelshtein+AUR@gmail.com>
# Unofficial Maintainer: you-oopsdev <The profile (AUR) has all the contacts for communication or write to GitHub>
# Contributor:: Ilya Basin <basinilya at gmail dot com>

pkgname=3proxy
pkgver=0.9.4
pkgrel=4
pkgdesc="A tiny crossplatform proxy server"
arch=('any')
url="http://www.3proxy.ru/"
license=('BSD')
depends=('glibc')
makedepends=('gcc' 'make')
backup=(etc/3proxy/3proxy.cfg
        etc/logrotate.d/3proxy)
source=("$pkgname-$pkgver.tar.gz::https://github.com/z3APA3A/$pkgname/archive/$pkgver.tar.gz"
        "$pkgname.service"
        "sysusers"
        "tmpfiles"
        "logrotate.3proxy"
	"https://gitlab.alpinelinux.org/alpine/aports/-/raw/87e5ea0bdf650320199ba07cbd7019d81540490c/testing/3proxy/gcc14.patch"
)
sha256sums=('b497f74d6cc7ee58ff824457427acc02c6f7a102e483816fbb1b2494942ef983'
            'bcaaf2e995dec828cac9cc8c0efe9deab9a369f476b502b2a8ef1e58cb9f4eda'
            '862161b0e139a0c501b7c1d1941189234018d9730013f3bb23797a7893a098f5'
            '790126915b39e5838ff77e2f416f3652ca69a380282f60c82a7c2f8eae516094'
            'bf5933cc2fc13fc5de633d23dad7651188af8e740f03a468f2a86df6dc4d2ef8'
            'ce535a98468e51144ef2a78bbce53d33824b96a1b474bfb076b44a945fa93a5a')

prepare() {
  cd "$srcdir/$pkgname-$pkgver"
#Backup file
  cp -p ./Makefile.Linux ./Makefile.Linux.bak
###----> See:https://gitweb.gentoo.org/repo/gentoo.git/tree/net-proxy/3proxy/files/3proxy-0.9.0-gentoo.patch
  echo -e "\e[1;34m->\033[0m \e[1;37mPatching Makefile for Linux...\033[0m"
  sed -i -e "s/CFLAGS = -g  -fPIC -O2 -fno-strict-aliasing -c -pthread -DWITHSPLICE -D_GNU_SOURCE -DGETHOSTBYNAME_R -D_THREAD_SAFE -D_REENTRANT -DNOODBC -DWITH_STD_MALLOC -DFD_SETSIZE=4096 -DWITH_POLL -DWITH_NETFILTER/CFLAGS += -fPIC -fno-strict-aliasing -c -pthread -DWITHSPLICE -D_GNU_SOURCE -DGETHOSTBYNAME_R -D_THREAD_SAFE -D_REENTRANT -DNOODBC -DWITH_STD_MALLOC -DFD_SETSIZE=4096 -DWITH_POLL -DWITH_NETFILTER/" ./Makefile.Linux #Fix CFLAGS
  sed -i -e "s/LDFLAGS = -fPIE -O2 -fno-strict-aliasing -pthread/LDFLAGS += -fPIE -fno-strict-aliasing -pthread/" ./Makefile.Linux  #Fix LDFLAGS
  sed -i -e "s/CC = gcc/CC ?= gcc/" ./Makefile.Linux
  sed -i -e "s/LN = gcc/LN ?= gcc/" ./Makefile.Linux
  #sed -i '137,$d' ./Makefile
#Fix build with gcc 14
patch -Np1 -i $srcdir/gcc14.patch
}

build() {
  cd "$srcdir/$pkgname-$pkgver"
#For compatibility
  ln -s ./Makefile.Linux ./Makefile
  make prefix=/usr DESTDIR="$pkgdir" ETCDIR="/etc/$pkgname" INITDIR="/etc/init.d" BINDIR="/usr/bin"
}

package() {
  cd "$pkgname-$pkgver"
  make install DESTDIR="$pkgdir" ETCDIR="$pkgdir/etc/$pkgname"  INITDIR="$pkgdir/etc/init.d" BINDIR="$pkgdir/usr/bin"
#Fix name binary See ---> https://github.com/BlackArch/blackarch/blob/c950db958b3d3ef212b185e46707838798ca9557/packages/3proxy/PKGBUILD#L40
  mv "$pkgdir/usr/bin/proxy" "$pkgdir/usr/bin/$pkgname-proxy"
## Fix conflict with extra/libproxy. See ---> https://github.com/BlackArch/blackarch/blob/4036d65c1e7b554c5d509f966efff8a2e632d231/packages/3proxy/PKGBUILD#L62
  mv -vf "$pkgdir/usr/share/man/man8/proxy.8" \
    "$pkgdir/usr/share/man/man8/$pkgname-proxy.8"

  install -dm 755 "$pkgdir/usr/lib/systemd/system/" \
                  "$pkgdir/usr/lib/$pkgname" \
                  "$pkgdir/usr/share/licenses/$pkgname/" \
                  "$pkgdir/etc/logrotate.d/"
  mv "$pkgdir"/usr/local/3proxy/libexec/*.so "$pkgdir"/usr/lib/$pkgname
  rm -r "$pkgdir"/usr/local/ \
        "$pkgdir"/etc/3proxy/* \
        "$pkgdir"/etc/init.d/ \
        "$pkgdir"/var/
#Will fix the permissions for us. (see /usr/lib/tmpfiles.d/3proxy.conf)
  install -d -o root -g root -m 755 "$pkgdir"/var/log/3proxy
  install -Dm644 "$srcdir/3proxy.service" "$pkgdir/usr/lib/systemd/system/"
  touch "$pkgdir/etc/$pkgname/$pkgname.cfg" && chmod 600 "$pkgdir/etc/$pkgname/$pkgname.cfg"
  install -Dm644 "$srcdir/"sysusers "$pkgdir"/usr/lib/sysusers.d/$pkgname.conf
  install -Dm644 "$srcdir/"tmpfiles "$pkgdir"/usr/lib/tmpfiles.d/$pkgname.conf
  install -Dm600 "$srcdir/$pkgname-$pkgver"/cfg/$pkgname.cfg.sample "$pkgdir/etc/$pkgname/"
  mv "$srcdir/$pkgname-$pkgver/copying" "$pkgdir/usr/share/licenses/$pkgname/"
#For logroting log's files
  install -Dm644 "$srcdir/"logrotate.$pkgname "$pkgdir"/etc/logrotate.d/$pkgname
}
