# Building Dependencies without `sudo` in Travis CI

#### Why
* Travis CI discourages using `sudo`.
  * Container-Based Envionment:
    * No support for `sudo`, `setuid`, `setgid`

  * SUDO-Enabled Envionment:
    * You need to set `sudo: required` to enable `sudo`

* Container-Based Envionment is much faster than SUDO-Enabled(boot time: 1-6s vs 20-52s).

#### Solution

* Set `--prefix=$HOME` when run `./configure`.

* Replace `sudo make install` with `make install`.

* Set `LD_LIBRARY_PATH`, `CFLAGS`, `LDFLAGS` before run your script if it needs to load dynamic library.

#### Example [.travis.yml](https://github.com/northbright/xls2csv/blob/master/.travis.yml) for C Project which Requires [libxls](http://libxls.sourceforge.net/).

    language: c

    compiler: gcc

    before_install:

    install:
      - wget http://downloads.sourceforge.net/libxls/libxls-0.2.0.tar.gz
      - tar -xzvf libxls-0.2.0.tar.gz
      - cd libxls-0.2.0
      - ./configure --prefix=$HOME
      - make
      - make install

    before_script:
      - export LD_LIBRARY_PATH="$HOME/lib":$LD_LIBRARY_PATH

    script:
      - cd ../src
      - gcc -o xls2csv *.c -I$HOME/include -L/$HOME/lib -lxlsreader

#### Example [.travis.yml](https://github.com/northbright/xls2csv-go/blob/master/.travis.yml) for [Go](https://golang.org) Project which Requires [libxls](http://libxls.sourceforge.net/).

    language: go

    go:
      - "1.3.3"
      - "1.4.2"
      - "1.5"
      - "1.6"
      - "1.7"
      - "1.8"
      - tip

    before_install:

    install:
      - wget http://downloads.sourceforge.net/libxls/libxls-0.2.0.tar.gz
      - tar -xzvf libxls-0.2.0.tar.gz
      - cd libxls-0.2.0
      - ./configure --prefix=$HOME
      - make
      - make install
      - go get github.com/northbright/pathhelper

    before_script:
      - export LD_LIBRARY_PATH="$HOME/lib":$LD_LIBRARY_PATH

    script:
      - cd ../xls2csv
      - CGO_CFLAGS="-I$HOME/include" CGO_LDFLAGS="-L$HOME/lib -lxlsreader" go test -c && ./xls2csv.test


#### References
* [How to build library without sudo?](http://www.howtobuildsoftware.com/index.php/how-do/bSGZ/ld-travis-ci-configure-travis-how-to-build-library-without-sudo)
* [How can I run Haxe 2.10 on Travis-CI (64-bit Ubuntu)?](http://stackoverflow.com/questions/27137351/how-can-i-run-haxe-2-10-on-travis-ci-64-bit-ubuntu)
* [Environment Variables](https://docs.travis-ci.com/user/environment-variables/)
* [The Build Environment](https://docs.travis-ci.com/user/ci-environment/)
