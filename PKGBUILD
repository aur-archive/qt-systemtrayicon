pkgname=qt-systemtrayicon
pkgver=4.7.3
pkgrel=1
pkgdesc='A cross-platform application and UI framework'
arch=('i686' 'x86_64')
url='http://qt.nokia.com/'
license=('GPL3' 'LGPL')
depends=('libtiff' 'libpng' 'libmng' 'sqlite3' 'ca-certificates' 'glib2' 'dbus'
      'fontconfig' 'libgl' 'libsm' 'libxrandr' 'libxv' 'libxi' 'alsa-lib'
      'xdg-utils' 'hicolor-icon-theme' 'desktop-file-utils')
makedepends=('mesa' 'postgresql-libs' 'mysql' 'unixodbc' 'cups' 'gtk2')
optdepends=('postgresql-libs: PostgreSQL driver'
	    'libmysqlclient: MySQL driver'
	    'unixodbc: ODBC driver'
	    'libxinerama: Xinerama support'
	    'libxcursor: Xcursor support'
	    'libxfixes: Xfixes support')
options=('!libtool')
_pkgfqn="qt-everywhere-opensource-src-${pkgver}"
source=("ftp://ftp.qt.nokia.com/qt/source/${_pkgfqn}.tar.gz"
        'assistant.desktop' 'designer.desktop' 'linguist.desktop'
        'qtconfig.desktop'
        'qtbug-16292.patch' 'kubuntu_14_systemtrayicon.diff')
md5sums=('49b96eefb1224cc529af6fe5608654fe'
         'fc211414130ab2764132e7370f8e5caa'
         '85179f5e0437514f8639957e1d8baf62'
         'f11852b97583610f3dbb669ebc3e21bc'
         '6b771c8a81dd90b45e8a79afa0e5bbfd'
         'dc7ed8c2e8c68a175f7f05a34dccc937'
         '04981900763bb5e93dea7f2c5e98247f')
provides=("qt=${pkgver}")
conflicts=('qt')
install='qt-systemtrayicon.install'

build() {
	unset QMAKESPEC
	export QT4DIR=$srcdir/$_pkgfqn
	export PATH=${QT4DIR}/bin:${PATH}
	export LD_LIBRARY_PATH=${QT4DIR}/lib:${LD_LIBRARY_PATH}

    # FS#24601
    export CXXFLAGS="$CXXFLAGS -fno-strict-aliasing"

	cd $srcdir/$_pkgfqn

    # Already fixed upstream
    patch -p1 -i "${srcdir}"/qtbug-16292.patch
    patch -p1 -i "${srcdir}"/kubuntu_14_systemtrayicon.diff
    sed -i "s|-O2|$CXXFLAGS|" mkspecs/common/g++.conf
	sed -i "/^QMAKE_RPATH/s| -Wl,-rpath,||g" mkspecs/common/g++.conf
	sed -i "/^QMAKE_LFLAGS\s/s|+=|+= $LDFLAGS|g" mkspecs/common/g++.conf

	./configure -confirm-license -opensource \
		-prefix /usr \
		-docdir /usr/share/doc/qt \
		-plugindir /usr/lib/qt/plugins \
		-importdir /usr/lib/qt/imports \
		-datadir /usr/share/qt \
		-translationdir /usr/share/qt/translations \
		-sysconfdir /etc \
		-examplesdir /usr/share/doc/qt/examples \
		-demosdir /usr/share/doc/qt/demos \
		-largefile \
		-plugin-sql-{psql,mysql,sqlite,odbc} \
		-system-sqlite \
		-xmlpatterns \
		-no-phonon \
		-no-phonon-backend \
		-svg \
		-webkit \
		-script \
		-scripttools \
		-system-zlib \
		-system-libtiff \
		-system-libpng \
		-system-libmng \
		-system-libjpeg \
		-nomake demos \
		-nomake examples \
		-nomake docs \
		-no-rpath \
		-openssl-linked \
		-silent \
		-optimized-qmake \
		-dbus \
		-reduce-relocations \
		-no-separate-debug-info \
		-gtkstyle \
		-opengl \
		-no-openvg \
		-glib
	make
}

package() {
	
    cd $srcdir/$_pkgfqn
	make INSTALL_ROOT=$pkgdir install

	# install missing icons and desktop files
	for icon in tools/linguist/linguist/images/icons/linguist-*-32.png ; do
		size=$(echo $(basename ${icon}) | cut -d- -f2)
		install -p -D -m644 ${icon} ${pkgdir}/usr/share/icons/hicolor/${size}x${size}/apps/linguist.png
	done
	install -p -D -m644 src/gui/dialogs/images/qtlogo-64.png ${pkgdir}/usr/share/icons/hicolor/64x64/apps/qtlogo.png
	install -p -D -m644 tools/assistant/tools/assistant/images/assistant.png ${pkgdir}/usr/share/icons/hicolor/32x32/apps/assistant.png
	install -p -D -m644 tools/designer/src/designer/images/designer.png ${pkgdir}/usr/share/icons/hicolor/128x128/apps/designer.png
	install -d ${pkgdir}/usr/share/applications
	install -m644 ${srcdir}/{linguist,designer,assistant,qtconfig}.desktop ${pkgdir}/usr/share/applications/

	# install license addition
	install -D -m644 LGPL_EXCEPTION.txt ${pkgdir}/usr/share/licenses/qt/LGPL_EXCEPTION.txt

	# Fix wrong path in pkgconfig files
	find ${pkgdir}/usr/lib/pkgconfig -type f -name '*.pc' \
		-exec perl -pi -e "s, -L${srcdir}/?\S+,,g" {} \;
	# Fix wrong path in prl files
	find ${pkgdir}/usr/lib -type f -name '*.prl' \
		-exec sed -i -e '/^QMAKE_PRL_BUILD_DIR/d;s/\(QMAKE_PRL_LIBS =\).*/\1/' {} \;
}

