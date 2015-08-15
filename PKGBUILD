# Maintainer: Aaron Ali <t0nedef@causal.ca>
# Contributor: Alexander De Sousa <archaur.xandy21@spamgourmet.com>
# Contributor: Vasco Costa <vasco dot costa at geekslot dot com>
# Contributor: Michael Jones <jmjones at sisna dot com>
# Contributor: Valerio Mariani <valerio dot mariani at hispeed dot ch>

pkgname=cisco-vpnclient-amdphenom
_opkgname=cisco-vpnclient
_pkgname=vpnclient
pkgver=4.8.02.0030_k9
_pkgver=4.8.02.0030-k9
pkgrel=8
pkgdesc="Client to access Cisco VPNs (Virtual Private Network) with patch for AMD Phenom"
arch=(i686 x86_64)
_kernver=`uname -r`
url="http://www.cisco.com/en/US/products/sw/secursw/ps2308/"
license=("custom")
install=install
provides=("$_opkgname")
conflicts=("$_opkgname")
if [ "`uname -m`" = "i686" ]; then
	depends=('linux-lts' 'dkms')
else
	depends=('linux-lts' 'lib32-gcc-libs' 'dkms')
fi
makedepends=('linux-lts-headers')
source=(
	http://projects.tuxx-home.at/ciscovpn/clients/linux/4.8.02/$_pkgname-linux-x86_64-$_pkgver.tar.gz
	fixes.patch
	ciscovpn@.service
	dkms.conf
)
md5sums=(
	'3ccc9641fd7dae29bfc90556ae5c4b74'
	'd15a8a84a6a6ea3ed2da3bf16c1eb287'
	'f525edc2cb1afae283114d7b2c61c64f'
	'3c82f95fe1d4aedb03e0acd9c5099b1a'
)

build() {
	cd "$srcdir/$_pkgname"
	msg2 "Applying patches..."
	patch -Nup1 -i ../fixes.patch
	msg2 "Making a copy for dkms..."
	mkdir orig
	cp * orig/. || echo -n
	msg2 "Building..."
	KBUILD_NOPEDANTIC=1 make
}

package() {
	cd "$srcdir/$_pkgname"
	msg2 "Installing files and creating symlinks..."
	install -dm755 "$pkgdir/opt/$_opkgname/bin"
	install -Dm755 $_pkgname cvpnd ipseclog cisco_cert_mgr "$pkgdir/opt/$_opkgname/bin/"
	install -Dm755 libvpnapi.so "$pkgdir/opt/$_opkgname/lib/libvpnapi.so"
	install -Dm644 vpnapi.h "$pkgdir/opt/$_opkgname/include/vpnapi.h"
	install -Dm644 $_pkgname.ini "$pkgdir/opt/$_opkgname/$_pkgname.ini"
	install -Dm644 sample.pcf "$pkgdir/opt/$_opkgname/Profiles/sample.pcf"
	install -dm755 "$pkgdir/opt/$_opkgname/Certificates"
	install -Dm644 cisco_ipsec.ko "$pkgdir/lib/modules/$_kernver/extramodules/CiscoVPN/cisco_ipsec.ko"
	install -Dm644 license.txt "$pkgdir/usr/share/licenses/$_opkgname/license.txt"
	install -dm755 "$pkgdir/usr/bin"
	install -dm755 "$pkgdir/etc"
	ln -sf "/opt/$_opkgname/bin/$_pkgname" "$pkgdir/usr/bin/$_pkgname"
	ln -sf "/opt/$_opkgname/bin/cisco_cert_mgr" "$pkgdir/usr/bin/cisco_cert_mgr"
	ln -sf "/opt" "$pkgdir/etc/opt"
	msg2 "Updating install file..."
	sed -i -e "s/KERNEL_VERSION=.*/KERNEL_VERSION=$_kernver/g" "$startdir/install"
	install -Dm644 ../ciscovpn@.service "$pkgdir/usr/lib/systemd/system/ciscovpn@.service"
	install -dm755 "$pkgdir/usr/src"
	cp -r orig "$pkgdir/usr/src/${_opkgname}-${pkgver}"
	install -Dm644 ../dkms.conf "$pkgdir/usr/src/${_opkgname}-${pkgver}/dkms.conf"
}

