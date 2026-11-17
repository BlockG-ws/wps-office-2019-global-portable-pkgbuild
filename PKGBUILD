# Maintainer: GrassBlock <i at gb0 dont dev>

pkgname=wps-office-2019-global-portable
pkgver=11.1.0.11723
pkgrel=2
pkgdesc="Kingsoft Office (WPS Office), repackaged from the Flathub manifest and sandboxed by Portable"
arch=('x86_64')
url="https://www.wps.com/linux"
license=('LicenseRef-WPS-EULA')
# AUR freetype2-wps patches restore scale-based fake-bold rendering for WPS.
# https://aur.archlinux.org/packages/freetype2-wps
_freetypever=2.13.3
license+=('FTL OR GPL-2.0-or-later')
options=('!strip' '!debug' '!emptydirs')
provides=('wps-office' 'wps-office-mime')
conflicts=('wps-office' 'wps-office-mime' 'wps-office-cn' 'wps-office-365-portable' 'kingsoft-office' 'wps-office-cn-bwrap')
makedepends=('portable-packer' 'binutils' 'xz' 'meson' 'librsvg' 'qt5-base')
depends=(
	'bash' 'python' 'fontconfig' 'freetype2' 'glu' 'gtk3' 'hicolor-icon-theme'
	'libjpeg-turbo' 'libpulse' 'libtool' 'libx11' 'libxext' 'libxkbcommon'
	'libxkbcommon-x11' 'libxml2-legacy' 'libxrender' 'libxss' 'libxslt'
	'portable' 'sqlite' 'xdg-utils'
	'brotli' 'bzip2' 'harfbuzz' 'libpng' 'zlib'
)
optdepends=(
	'libtiff5: PDF support (WPS PDF)'
	'cups: printing support'
	'fcitx5: input method support'
	'ttf-wps-fonts: symbol fonts used by WPS Office documents'
	'wps-office-fonts: Founder Chinese fonts'
)
source=("https://wdl1.pcfg.cache.wpscdn.com/wpsdl/wpsoffice/download/linux/${pkgver##*.}/wps-office_${pkgver}.XA_amd64.deb"
	config.toml
	wps-launch
	'com.wps.Office.desktop'
	'com.wps.Office.wps.desktop'
	'com.wps.Office.et.desktop'
	'com.wps.Office.wpp.desktop'
	'com.wps.Office.pdf.desktop'
	'com.wps.Office.metainfo.xml'
	"https://download-mirror.savannah.gnu.org/releases/freetype/freetype-${_freetypever}.tar.xz"{,.sig}
	'0000-WPS-compatiblity.patch::https://gitlab.freedesktop.org/freetype/freetype/-/merge_requests/304/diffs.patch'
	'https://gitlab.archlinux.org/archlinux/packaging/packages/freetype2/-/raw/29bc205851a24e5bdabc6998e825aab4518eb6df/0001-Enable-table-validation-modules.patch'
	'https://gitlab.archlinux.org/archlinux/packaging/packages/freetype2/-/raw/29bc205851a24e5bdabc6998e825aab4518eb6df/0002-Enable-subpixel-rendering.patch'
	'https://gitlab.archlinux.org/archlinux/packaging/packages/freetype2/-/raw/29bc205851a24e5bdabc6998e825aab4518eb6df/0003-Enable-long-PCF-family-names.patch')
sha256sums=('fe6326210f69d94efdbf2728914d293036be391b93a614f58cd0e1ff1d4923b3'
	'SKIP'
	'SKIP'
	'SKIP'
	'SKIP'
	'SKIP'
	'SKIP'
	'SKIP'
	'SKIP'
	'SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP')
b2sums=('SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP'
	'f9591c6998df02b072adaf38a968e91deae8ed4d53ea0cb74d08982c4f0e48b1a98c1378a698164e4f730f07a3b0bea308a94fcc2e2b8ce9967dbf9478b599bd'
	'SKIP'
	'42e53690cae9930fdfc819ecbce578619b4bbc75faab247dc09394abaf768d58cc9b8314f5bb22e11a991611135d50bd364c1b45d1f871e26b82dbadda99657c'
	'2c148694e150b3faae9dc46ead824ae6d94cfe34f2918bc9066f45bab7e6b6f77b4d4b2fee00c3d466d866e1985132cea4a774dcf1bab95409b7cf55efff01e1'
	'9598d13eae0411878c1af8e5d875c3b1fc09f4e6649edfbbf3b0a819c4c585648521f0b30ffd1318f5106cfde9c899d3db70c8ffe36dac59f3f98b5fd7dab115'
	'66deb179d1f1f4e2e35f6d50acfbacce80595d5128f5fed8c1871838c210dbf1a7173a87dd937d64997844c8f478c8f81120f71e33b9d59d980e179d103ff31c')
validpgpkeys=('E30674707856409FF1948010BE6C3AAC63AD8E3F') # Werner Lemberg

prepare() {
	cd "${srcdir}"

	ar p "wps-office_${pkgver}.XA_amd64.deb" data.tar.xz > data.tar.xz
	mkdir -p deb
	bsdtar -xJf data.tar.xz -C deb

	# WPS unquotes file:// URLs with Python 2 code
	python - "${srcdir}/deb/usr/bin/wps" <<'EOF'
import sys
p = sys.argv[1]
s = open(p).read()
s = s.replace(
	"python -c 'import sys, urllib; print urllib.unquote(sys.argv[1])'",
	"python3 -c 'import sys, urllib.parse; print(urllib.parse.unquote(sys.argv[1]))'")
open(p, 'w').write(s)
EOF

	cd "${srcdir}/freetype-${_freetypever}"
	patch -Np1 -i ../0000-WPS-compatiblity.patch
	patch -Np1 -i ../0001-Enable-table-validation-modules.patch
	patch -Np1 -i ../0002-Enable-subpixel-rendering.patch
	patch -Np1 -i ../0003-Enable-long-PCF-family-names.patch
}

build() {
	arch-meson "freetype-${_freetypever}" freetype-build \
		-D freetype2:default_library=shared
	meson compile -C freetype-build
}

check() {
	meson test -C freetype-build --print-errorlogs
}

package() {
	cd "${srcdir}/deb"

	install -d "${pkgdir}/opt/kingsoft"
	cp -a opt/kingsoft/wps-office "${pkgdir}/opt/kingsoft/"

	# WPS resolves this private library through its existing $ORIGIN RPATH.
	install -Dm644 "${srcdir}/freetype-build/libfreetype.so.6" \
		"${pkgdir}/opt/kingsoft/wps-office/office6/libfreetype.so.6"
	install -Dm644 "${srcdir}/freetype-${_freetypever}/docs/FTL.TXT" \
		"${pkgdir}/usr/share/licenses/${pkgname}/FTL.TXT"

	# Launchers from the deb live next to office6 and take care of templates
	# and cloud URLs
	install -Dm755 usr/bin/wps usr/bin/et usr/bin/wpp usr/bin/wpspdf \
		-t "${pkgdir}/opt/kingsoft/wps-office/"

	# Use the system copies of these, the bundled ones are older than the
	# distribution's ABI requirements
	rm -f "${pkgdir}/opt/kingsoft/wps-office/office6/libstdc++.so"*
	rm -f "${pkgdir}/opt/kingsoft/wps-office/office6/libjpeg.so"*
	rm -f "${pkgdir}/opt/kingsoft/wps-office/office6/libbz2.so"*

	# The cloud service is unusable without network access and crashes on
	# start-up otherwise
	rm -f "${pkgdir}/opt/kingsoft/wps-office/office6/wpscloudsvr"

	install -d "${pkgdir}/usr/share"
	cp -a usr/share/icons usr/share/mime usr/share/fonts "${pkgdir}/usr/share/"

	# Upstream puts the application icons in the mimetypes category
	local d
	for d in "${pkgdir}"/usr/share/icons/hicolor/*; do
		if compgen -G "${d}/mimetypes/wps-office2019-*.png" > /dev/null; then
			install -d "${d}/apps"
			cp -a "${d}"/mimetypes/wps-office2019-*.png "${d}/apps/"
		fi
	done

	install -Dm644 opt/kingsoft/wps-office/office6/mui/default/*.html \
		-t "${pkgdir}/usr/share/licenses/${pkgname}/"

	# Temporary desktop entry, Portable replaces it with its own copy
	install -Dm644 "${srcdir}/com.wps.Office.desktop" \
		"${pkgdir}/usr/share/applications/com.wps.Office.desktop"

	export pkgdir pkgname srcdir
	portable-packer \
		--distro arch \
		--mode post \
		--config-ng "${srcdir}/config.toml" \
		--desktop-file "${srcdir}/com.wps.Office.desktop"

	# Application selectors for the remaining suite entries
	install -m644 "${srcdir}"/com.wps.Office.{wps,et,wpp,pdf}.desktop \
		-t "${pkgdir}/usr/share/applications/"

	install -Dm644 "${srcdir}/com.wps.Office.metainfo.xml" \
		"${pkgdir}/usr/share/metainfo/com.wps.Office.metainfo.xml"

	install -Dm755 "${srcdir}/wps-launch" \
		"${pkgdir}/usr/lib/portable/info/com.wps.Office/launch"
}
