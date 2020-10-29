#Maintainer: naruto522ru <>
pkgname=3proxy
pkgver=0.9.0
pkgrel=1
pkgdesc="A tiny crossplatform proxy server"
arch=('any')
url="http://www.3proxy.ru/"
license=('BSD')
depends=('glibc')
makedepends=('gcc' 'make')
backup=('etc/3proxy/3proxy.cfg')
source=("$pkgname-$pkgver.tar.gz::https://github.com/z3APA3A/$pkgname/archive/$pkgver.tar.gz"
        "$pkgname.service"
	"sysusers"
	"tmpfiles"
)
md5sums=('d47099e82914d854daac4688740d625c' #tarboll 3proxy
         '99fbf305116df79fde910402c1132295' #3proxy.service
	 '6cafc741aa7ca8aab877f24a132c8bd1' #sysuser
	 '4a566e594c1541f65d0366e1f618b5ce' #tmpfiles
)

prepare() {
	cd "$srcdir/$pkgname-$pkgver"
	#For compatibility
        ln -s ./Makefile.Linux ./Makefile
	#Backup file
	cp -p ./Makefile.Linux ./Makefile.Linux.bak
        ###----> See:https://gitweb.gentoo.org/repo/gentoo.git/tree/net-proxy/3proxy/files/3proxy-0.9.0-gentoo.patch
	echo -e "  \e[1;34m->\033[0m \e[1;37mPatching Makefile for Linux...\033[0m"
	sed --follow-symlinks -i -e "s/CFLAGS = -g  -fPIC -O2 -fno-strict-aliasing -c -pthread -DWITHSPLICE -D_GNU_SOURCE -DGETHOSTBYNAME_R -D_THREAD_SAFE -D_REENTRANT -DNOODBC -DWITH_STD_MALLOC -DFD_SETSIZE=4096 -DWITH_POLL -DWITH_NETFILTER/CFLAGS += -fPIC -fno-strict-aliasing -c -pthread -DWITHSPLICE -D_GNU_SOURCE -DGETHOSTBYNAME_R -D_THREAD_SAFE -D_REENTRANT -DNOODBC -DWITH_STD_MALLOC -DFD_SETSIZE=4096 -DWITH_POLL -DWITH_NETFILTER/" ./Makefile #Fix CFLAGS
	sed --follow-symlinks -i -e "s/LDFLAGS = -fPIE -O2 -fno-strict-aliasing -pthread/LDFLAGS += -fPIE -fno-strict-aliasing -pthread/" ./Makefile  #Fix LDFLAGS
	sed --follow-symlinks -i -e "s/CC = gcc/CC ?= gcc/" ./Makefile
	sed --follow-symlinks -i -e "s/LN = gcc/LN ?= gcc/" ./Makefile
	sed -i '137,$d' ./Makefile
}

build() {
	cd "$srcdir/$pkgname-$pkgver"
	make prefix=/usr DESTDIR="$pkgdir" ETCDIR="/etc/$pkgname" INITDIR="/etc/init.d" BINDIR="/usr/bin"
}

package() {
	cd "$pkgname-$pkgver"
	make install DESTDIR="$pkgdir" ETCDIR="$pkgdir/etc/$pkgname"  INITDIR="$pkgdir/etc/init.d" BINDIR="$pkgdir/usr/bin"
        #Fix name binary See ---> https://github.com/BlackArch/blackarch/blob/01cbd6e1c796e32f901ecd3bd46f012a06026b34/packag>
        mv "$pkgdir/usr/bin/proxy" "$pkgdir/usr/bin/$pkgname-proxy"
        install -dm 755 "$pkgdir/usr/lib/systemd/system/"
	install -dm 755 "$pkgdir/usr/lib/"
	install -dm 755 "$pkgdir/usr/share/licenses/$pkgname/"
	mv "$pkgdir"/usr/local/3proxy/libexec/*.so "$pkgdir"/usr/lib/
	rm -r "$pkgdir"/usr/local/
        rm "$pkgdir"/etc/3proxy/*
        rm -r "$pkgdir"/var/
        # Will fix the permissions for us. (see /usr/lib/tmpfiles.d/3proxy.conf)
        install -d -o root -g root -m 755 "$pkgdir"/var/log/3proxy
        install -Dm644 "$srcdir/3proxy.service" "$pkgdir/usr/lib/systemd/system/"
	touch "$pkgdir/etc/$pkgname/$pkgname.cfg" && chmod 600 "$pkgdir/etc/$pkgname/$pkgname.cfg"
	install -Dm644 "$srcdir/"sysusers "$pkgdir"/usr/lib/sysusers.d/$pkgname.conf
        install -Dm644 "$srcdir/"tmpfiles "$pkgdir"/usr/lib/tmpfiles.d/$pkgname.conf
	install -Dm600 "$srcdir/$pkgname-$pkgver"/cfg/$pkgname.cfg.sample "$pkgdir/etc/$pkgname/"
	mv "$srcdir/$pkgname-$pkgver/copying" "$pkgdir/usr/share/licenses/$pkgname/"
}
