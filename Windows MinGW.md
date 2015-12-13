# 1. Prepare your build system.
## 1.1 Set up a clean VM
I strongly suggest setting up a clean windows virtual machine via Virtualbox or similar.

## 1.2 Install msys shell
http://sourceforge.net/projects/mingw/files/Installer/mingw-get-setup.exe/download

From MinGW installation manager -> All packages -> MSYS mark only the following for installation:

`msys-base-bin`

Unselect and make sure no mingw packages are checked for installation or present from a previous install. Only the above msys packages should be installed. Also make sure that msys-gcc and msys-w32api packages are not installed.

Then click on Installation -> Apply changes

## 1.3 Install Perl:
http://downloads.activestate.com/ActivePerl/releases/5.18.1.1800/ActivePerl-5.18.1.1800-MSWin32-x64-297570.msi
You can exclude Perl Script, PPM, documentation and examples if you don't need them for other purposes.

## 1.4 Install MinGW-builds project toolchain
Download http://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win32/Personal%20Builds/mingw-builds/4.9.2/threads-posix/dwarf/i686-4.9.2-release-posix-dwarf-rt_v3-rev1.7z/download

and unpack it to C:\

## 1.5
Ensure that mingw-builds bin folder is set in your PATH environment variable. On Windows 7 your path should look something like:

`C:\mingw32\bin;C:\Perl64\site\bin;C:\Perl64\bin;%SystemRoot%\system32;%SystemRoot%;%SystemRoot%\System32\Wbem;%SYSTEMROOT%\System32\WindowsPowerShell\v1.0\`

1. From the Desktop, right-click the Computer icon and select Properties. If you don't have a Computer icon on your desktop, click the Start button, right-click the Computer option in the Start menu, and select Properties.
2. Click the Advanced System Settings link in the left column.
3. In the System Properties window, click on the Advanced tab, then click the Environment Variables button near the bottom of that tab.
4. In the Environment Variables window (pictured below), highlight the Path variable in the "System variables" section and click the Edit button. Add or modify the path lines with the paths you want the computer to access. Each different directory is separated with a semicolon as shown below.

## 1.6 Additional checks
C:\MinGW\bin should contain nothing but mingw-get.exe.
Your gcc -v output should be:
```
$ gcc -v
Using built-in specs.
COLLECT_GCC=c:\mingw32\bin\gcc.exe
COLLECT_LTO_WRAPPER=c:/mingw32/bin/../libexec/gcc/i686-w64-mingw32/4.9.2/lto-wrapper.exe
Target: i686-w64-mingw32
Configured with: ../../../src/gcc-4.9.2/configure --host=i686-w64-mingw32 --build=i686-w64-mingw32 --target=i686-w64-mingw32 --prefix=/mingw32 --with-sysroot=/c/mingw492/i686-492-posix-dwarf-rt_v3-rev1/mingw32 --with-gxx-include-dir=/mingw32/i686-w64-mingw32/include/c++ --enable-shared --enable-static --disable-multilib --enable-languages=ada,c,c++,fortran,objc,obj-c++,lto --enable-libstdcxx-time=yes --enable-threads=posix --enable-libgomp --enable-libatomic --enable-lto --enable-graphite --enable-checking=release --enable-fully-dynamic-string --enable-version-specific-runtime-libs --disable-sjlj-exceptions --with-dwarf2 --disable-isl-version-check --disable-cloog-version-check --disable-libstdcxx-pch --disable-libstdcxx-debug --enable-bootstrap --disable-rpath --disable-win32-registry --disable-nls --disable-werror --disable-symvers --with-gnu-as --with-gnu-ld --with-arch=i686 --with-tune=generic --with-libiconv --with-system-zlib --with-gmp=/c/mingw492/prerequisites/i686-w64-mingw32-static --with-mpfr=/c/mingw492/prerequisites/i686-w64-mingw32-static --with-mpc=/c/mingw492/prerequisites/i686-w64-mingw32-static --with-isl=/c/mingw492/prerequisites/i686-w64-mingw32-static --with-cloog=/c/mingw492/prerequisites/i686-w64-mingw32-static --enable-cloog-backend=isl --with-pkgversion='i686-posix-dwarf-rev1, Built by MinGW-W64 project' --with-bugurl=http://sourceforge.net/projects/mingw-w64 CFLAGS='-O2 -pipe -I/c/mingw492/i686-492-posix-dwarf-rt_v3-rev1/mingw32/opt/include -I/c/mingw492/prerequisites/i686-zlib-static/include -I/c/mingw492/prerequisites/i686-w64-mingw32-static/include' CXXFLAGS='-O2 -pipe -I/c/mingw492/i686-492-posix-dwarf-rt_v3-rev1/mingw32/opt/include -I/c/mingw492/prerequisites/i686-zlib-static/include -I/c/mingw492/prerequisites/i686-w64-mingw32-static/include' CPPFLAGS= LDFLAGS='-pipe -L/c/mingw492/i686-492-posix-dwarf-rt_v3-rev1/mingw32/opt/lib -L/c/mingw492/prerequisites/i686-zlib-static/lib -L/c/mingw492/prerequisites/i686-w64-mingw32-static/lib -Wl,--large-address-aware'
Thread model: posix
gcc version 4.9.2 (i686-posix-dwarf-rev1, Built by MinGW-W64 project)
```
# 2. Download, unpack and build required dependencies.
## 2.1 OpenSSL
OpenSSL: http://www.openssl.org/source/openssl-1.0.1l.tar.gz
From a MinGw shell (C:\MinGW\msys\1.0\msys.bat), unpack the source archive with tar (this will avoid symlink issues) then configure and make:
```
cd /c/deps/
tar xvfz openssl-1.0.1l.tar.gz
cd openssl-1.0.1l
./Configure no-zlib no-shared no-dso no-krb5 no-camellia no-capieng no-cast no-cms no-dtls1 no-gost no-gmp no-heartbeats no-idea no-jpake no-md2 no-mdc2 no-rc5 no-rdrand no-rfc3779 no-rsax no-sctp no-seed no-sha0 no-static_engine no-whirlpool no-rc2 no-rc4 no-ssl2 no-ssl3 mingw
make
```

## 2.2 Berkeley DB
Berkeley DB: http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz
We'll use version 4.8 to preserve binary wallet compatibility.
From a MinGW shell unpack the source archive, configure and make:
```
cd /c/deps/
tar xvfz db-4.8.30.NC.tar.gz
cd db-4.8.30.NC/build_unix
../dist/configure --enable-mingw --enable-cxx --disable-shared --disable-replication
make
```

# 2.3 Boost
Boost: http://sourceforge.net/projects/boost/files/boost/1.57.0/
Download either the zip or the 7z archive, unpack boost inside your C:\deps folder, then bootstrap and compile from a Windows command prompt:
```
cd C:\deps\boost_1_57_0\
bootstrap.bat mingw
b2 --build-type=complete --with-chrono --with-filesystem --with-program_options --with-system --with-thread toolset=gcc variant=release link=static threading=multi runtime-link=static stage
```
This will compile the required boost libraries and put them into the stage folder (C:\deps\boost_1_57_0\stage).
Note: make sure you don't use tarballs, as unix EOL markers can break batch files.

## 2.4 Miniupnpc
Miniupnpc: http://miniupnp.free.fr/files/download.php?file=miniupnpc-1.9.20150206.tar.gz
Unpack Miniupnpc to C:\deps, rename containing folder from "miniupnpc-1.9.20150206" to "miniupnpc" then from a Windows command prompt:
```
cd C:\deps\miniupnpc
mingw32-make -f Makefile.mingw init upnpc-static
```

## 2.5 qrencode
Download and unpack http://download.sourceforge.net/libpng/libpng-1.6.16.tar.gz inside your deps folder then configure and make:
```
cd /c/deps/libpng-1.6.16
configure --disable-shared
make
cp .libs/libpng16.a .libs/libpng.a
```

Download and unpack http://fukuchi.org/works/qrencode/qrencode-3.4.4.tar.gz inside your deps folder then configure and make:
```
cd /c/deps/qrencode-3.4.4

LIBS="../libpng-1.6.16/.libs/libpng.a ../../mingw32/i686-w64-mingw32/lib/libz.a" \
png_CFLAGS="-I../libpng-1.6.16" \
png_LIBS="-L../libpng-1.6.16/.libs" \
configure --enable-static --disable-shared --without-tools

make
```

## 2.6 Qt 4.8 libraries:
Download and unpack http://download.qt-project.org/official_releases/qt/4.8/4.8.6/qt-everywhere-opensource-src-4.8.6.zip
Note that due to a bug in qt 4.8.6 you will need to explicitly enable windows styles: https://bugreports.qt-project.org/browse/QTBUG-38706
Assuming qt sources have been unpacked to C:\Qt\4.8.6, from a windows command prompt:
```
cd C:\Qt\4.8.6
configure -release -opensource -confirm-license -static -no-sql-sqlite -no-qt3support -no-opengl -qt-zlib -no-gif -qt-libpng -qt-libmng -no-libtiff -qt-libjpeg -no-dsp -no-vcproj -no-openssl -no-dbus -no-phonon -no-phonon-backend -no-multimedia -no-audio-backend -no-webkit -no-script -no-scripttools -no-declarative -no-declarative-debug -qt-style-windows -qt-style-windowsxp -qt-style-windowsvista -no-style-plastique -no-style-cleanlooks -no-style-motif -no-style-cde -nomake demos -nomake examples
mingw32-make
```

# 3 Compile bitcoin-qt 0.8.6 with Qt 4.8:
Edit C:\bitcoin-0.8.6\bitcoin-qt.pro with your favourite text editor and add
dependency library locations:
```
# Dependency library locations can be customized with:
#    BOOST_INCLUDE_PATH, BOOST_LIB_PATH, BDB_INCLUDE_PATH,
#    BDB_LIB_PATH, OPENSSL_INCLUDE_PATH and OPENSSL_LIB_PATH respectively

BOOST_LIB_SUFFIX=-mgw49-mt-s-1_57
BOOST_INCLUDE_PATH=C:/deps/boost_1_57_0
BOOST_LIB_PATH=C:/deps/boost_1_57_0/stage/lib
BDB_INCLUDE_PATH=C:/deps/db-4.8.30.NC/build_unix
BDB_LIB_PATH=C:/deps/db-4.8.30.NC/build_unix
OPENSSL_INCLUDE_PATH=C:/deps/openssl-1.0.1j/include
OPENSSL_LIB_PATH=C:/deps/openssl-1.0.1j
MINIUPNPC_INCLUDE_PATH=C:/deps/
MINIUPNPC_LIB_PATH=C:/deps/miniupnpc
QRENCODE_INCLUDE_PATH=C:/deps/qrencode-3.4.4
QRENCODE_LIB_PATH=C:/deps/qrencode-3.4.4/.libs
```

flags for static build:
```
CONFIG += static
```

From a windows command prompt configure and make:
```
set PATH=%PATH%;C:\Qt\4.8.6\bin
cd C:\bitcoin-0.8.6\
qmake "USE_QRCODE=1" "USE_UPNP=1" "USE_IPV6=1" paycoin.pro
```
Well my apologies. 5 minutes after posting this I figured it out. Based on this, I figured out that I needed to add this line to my Makefile.Release generated by qmake.
```
DEFINES       = -DMINIUPNP_STATICLIB
```

From a windows command prompt configure and make:
```
mingw32-make -f Makefile.Release
```

TO be merged to make this possible
https://github.com/bitcoin/bitcoin/pull/2826/commits


References
https://bitcointalk.org/index.php?topic=149479.0
https://bitcointalk.org/index.php?topic=149479.msg11228704#msg11228704
https://web.archive.org/web/20140403091459/https://bitcointalk.org/index.php?topic=149479.0
https://bitcointalk.org/index.php?topic=149479.msg5950648#msg5950648
