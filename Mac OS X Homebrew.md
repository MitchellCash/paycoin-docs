# 1. Prepare your build system.
## 1.1 Install Homebrew

`ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

## 1.2 Install Qt

```
brew update
brew install qt
```

## 1.3 Install dependencies

```
brew update
brew install boost miniupnpc openssl berkeley-db4 protobuf
```

Note: After you have installed the dependencies, you should check that the Brew installed version of OpenSSL is the one available for compilation. You can check this by typing

```
openssl version
```

into Terminal. You should see OpenSSL 1.0.1e 11 Feb 2013.

```
brew link openssl --force
```

# 2 Compile Paycoin
# 2.1 Edit dependencies in paycoin.pro
Edit ~\paycoin\paycoin.pro with your favourite text editor and add
dependency library locations:

Add:

```
isEmpty(OPENSSL_INCLUDE_PATH) {
    macx:OPENSSL_INCLUDE_PATH = /usr/local/Cellar/openssl/1.0.2e/include
}
```

Change:

```
isEmpty(BDB_LIB_PATH) {
    macx:BDB_LIB_PATH = /usr/local/Cellar/berkeley-db4/4.8.30/lib
}

isEmpty(BDB_INCLUDE_PATH) {
    macx:BDB_INCLUDE_PATH = /usr/local/Cellar/berkeley-db4/4.8.30/include
}

isEmpty(BOOST_LIB_PATH) {
    macx:BOOST_LIB_PATH = /usr/local/Cellar/boost/1.59.0/lib
}

isEmpty(BOOST_INCLUDE_PATH) {
    macx:BOOST_INCLUDE_PATH = /usr/local/Cellar/boost/1.59.0/include
```

# 2.2 .moc files and Boost aren't friends

Wrap has_binary_operator.hpp in a #ifndef Q_MOC_RUN. Just insert #ifndef Q_MOC_RUN at line 8 and #endif at the end of the file.

```
--- has_binary_operator.hpp	Thu Oct 30 11:47:21 2014
+++ has_binary_operator.hpp	Mon Jan 05 23:58:51 2015
@@ -6,6 +6,7 @@
 //
 //  See http://www.boost.org/libs/type_traits for most recent version including documentation.

+#ifndef Q_MOC_RUN
 #include <boost/config.hpp>
 #include <boost/type_traits/ice.hpp>
 #include <boost/type_traits/integral_constant.hpp>
@@ -227,3 +228,4 @@
 #endif

 #include <boost/type_traits/detail/bool_trait_undef.hpp>
+#endif
```

# 2.3 Build time :)

Note: I still can't get miniupnpc working right so...

```
qmake USE_UPNP=-
make clean
make
```

# 2.4 To run the app:

`./Paycoin.app/Contents/MacOS/Paycoin`

References

https://bugreports.qt.io/browse/QTBUG-22829
http://stackoverflow.com/a/25194845
https://bitcointalk.org/index.php?topic=149479.msg10115012#msg10115012
https://github.com/bitcoin/bitcoin/blob/14f870cee50f791bfa9c08bf1d60f8a3b9aeab23/doc/readme-qt.md
