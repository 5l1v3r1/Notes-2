# Install libxml2 from Source on CentOS

## CentOS Versions
* CentOS 7
* CentOS 8

## Install Dependencies
* [zlib](https://www.zlib.net/)
   * [Install zlib on CentOS from Source](https://github.com/northbright/Notes/blob/master/zlib/install-zlib-on-centos-from-source.md) 
      * It'll install latest zlib to a new path: `/usr/local/zlib` which is the value of `--with-zlib` option

## Download Source on [official site](ftp://xmlsoft.org/libxml2/) or [github](https://github.com/GNOME/libxml2/releases)

    cd download
    wget https://github.com/GNOME/libxml2/archive/v2.9.9.tar.gz
    tar -xzvf v2.9.9.tar.gz
    cd libxml2-2.9.9

## Configure

```
./autogen.sh
```

```
LDFLAGS="-Wl,-rpath=/usr/local/zlib/lib" \
\
./configure --prefix=/usr/local/libxml2 \
--with-zlib=/usr/local/zlib
```

## Make and Install
```
make
sudo make install
```

## Check RPATH and linked libraries
```
readelf -d /usr/local/libxml2/lib/libxml2.so
ldd /usr/local/libxml2/lib/libxml2.so
```
