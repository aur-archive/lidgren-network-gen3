pkgname=lidgren-network-gen3
pkgver=2013.05.07
pkgrel=7
pkgdesc="Lidgren networking library generation 3"
arch=(any)
license=("MIT")
makedepends=(monogame dos2unix)
depends=(mono)
optdepends=("monogame: XNA extensions")
url="http://code.google.com/p/lidgren-network-gen3"
source=("http://lidgren-network-gen3.googlecode.com/files/lidgren-network-gen3-${pkgver//./-}.zip"
"Lidgren.Network.snk"
"use-monogame-for-xna.diff"
"lidgren.network.pc.in"
"lidgren.network.xna.pc.in")
md5sums=('81d10cea52d48608e0fa0cf9bdf4daf1'
         '1afa03d9408df2bb6af10bba740be9b8'
         '8f3c1516142563299105d117c221f8e9'
         '80e3b340597cc94fcd3eaa2c36274770'
         'e7cfdb2557c6dcc3d9de1d0fc5d05eb6')
         
prepare() {
  cd "$srcdir/Lidgren-network-gen3"
  find . -type f -exec dos2unix {} \;
  patch -Np0 "Lidgren XNA Extensions/Lidgren XNA Extensions/Lidgren XNA Extensions.csproj" \
    < "$srcdir/use-monogame-for-xna.diff"
}
         
build() {
	cd "$srcdir/Lidgren-network-gen3/Lidgren XNA Extensions"
	xbuild "Lidgren XNA Extensions.sln" /p:Configuration=Release
	cd "Lidgren XNA Extensions/bin/Release"
	rm {MonoGame.Framework,Tao.Sdl}.dll
	for file in `find . -name '*.dll'`; do
		monodis $file --output=${file%.dll}.il
	done
	for file in `find . -name '*.il'`; do
		ilasm /dll /key:$srcdir/Lidgren.Network.snk $file
	done
}

package() {
	cd "$srcdir/Lidgren-network-gen3/Lidgren XNA Extensions/Lidgren XNA Extensions/bin/Release"
	find . -name '*.dll' -o -name '*.mdb' -o -name '*.config' |
		xargs -rtl1 -I {} install -Dm644 {} "$pkgdir/usr/lib/lidgren.network/"{}
	mkdir -p "$pkgdir/usr/share/doc/lidgren.network"
	cp -r "$srcdir/Lidgren-network-gen3/Lidgren.Network/Documentation/"* "$pkgdir/usr/share/doc/lidgren.network/"
	find "$pkgdir/usr/lib/lidgren.network/" -name '*.dll' | xargs -rtl1 -I {} gacutil -i {} -root "$pkgdir/usr/lib"
	ln -s "/usr/lib/monogame.framework/MonoGame.Framework.dll" "$pkgdir/usr/lib/lidgren.network/MonoGame.Framework.dll"
	ln -s "/usr/lib/monogame.framework/Tao.Sdl.dll" "$pkgdir/usr/lib/lidgren.network/Tao.Sdl.dll"
	install -Dm644 "$srcdir/lidgren.network.pc.in" "$pkgdir/usr/lib/pkgconfig/lidgren.network.pc"
	install -m644 "$srcdir/lidgren.network.xna.pc.in" "$pkgdir/usr/lib/pkgconfig/lidgren.network.xna.pc"
	find "$pkgdir/usr/lib/pkgconfig" -type f -exec sed -i "s|@VERSION@|$pkgver|" {} \;
}
