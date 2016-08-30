---
layout: post
title: ENV
categories: [blog ]
tags: [book, first, ]
description: 环境部署
---
====
安装
====

**CentOS 6.5**

添加软件源
==========

.. code-block:: sh

   wget http://mirrors.hustunique.com/epel/6/i386/epel-release-6-8.noarch.rpm
   yum localinstall epel-release-6-8.noarch.rpm
   yum install centos-release-SCL

编译工具
========

.. code-block:: sh

   sudo su -c 'yum -y install gcc jemalloc-devel lua-devel git xz'

php
===

.. code-block:: sh

   sudo su -c 'yum -y install php54 php54-php-mbstring php54-php-mysqlnd'
   sudo su -c 'yum -y install php54-php-devel php54-php-gd openssl openssl-devel'

phalcon
-------

.. code-block:: sh

   # build and install phalcon
   git clone --depth=1 git://github.com/phalcon/cphalcon.git
   cd cphalcon/build
   sudo su -c './install'
   # load phalcon
   echo "extension=phalcon.so" > /opt/rh/php54/root/etc/php.d/phalcon.ini
   # restart php-fpm or httpd and so on

php baidu拼音扩展
-------
    https://github.com/BullSoft/php-pinyin
    注意在安装的最后需要改一下php.ini,加上pinyin.so

php-redis
---------

.. code-block:: sh

   sudo su -c 'pecl instal redis'
   sudo su -c 'pecl install igbinary'
   echo "extension=redis.so" > /opt/rh/php54/root/etc/php.d/redis.ini
   echo "extension=igbinary.so" > /opt/rh/php54/root/etc/php.d/igbinary.ini

php-mcrypt
----------

方法一
^^^^^^

编译安装之后，拷贝 mcrypt.so 和 mcrypt.ini 到对应的服务器

方法二
^^^^^^

.. code-block:: sh

   wget https://www.softwarecollections.org/repos/remi/php54more/epel-6-x86_64/php54-php-mcrypt-5.4.16-3.el6.x86_64.rpm
   sudo su -c 'yum localinstall php54-php-mcrypt-5.4.16-3.el6.x86_64.rpm'
   sudo su -c 'service php54-php-fpm restart'

extensions
----------

mysql
=====

.. code-block:: sh

   sudo su -c 'yum -y install mysql55-mysql-server'

nginx
=====

.. code-block:: sh

   echo '[nginx]
   name=nginx repo
   baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
   gpgcheck=0
   enabled=1' >> /etc/yum.repos.d/nginx.repo
   sudo su -c 'yum -y install nginx'

redis
=====

.. code-block:: sh

   wget http://download.redis.io/releases/redis-2.8.13.tar.gz
   tar xzf redis-2.8.13.tar.gz
   cd redis-2.8.13
   make
   sudo su -c 'make install'

gearmand
========

.. code-block:: sh

   sudo su -c 'yum -y install gearmand libgearman-devel'
   sudo su -c 'pecl install gearman'
   echo "extension=gearman.so" > /opt/rh/php54/root/etc/php.d/gearman.ini

memcached
=========

**必须启用 sasl 支持，否则无法使用基于用户名/密码的认证**

.. code-block:: sh

   sudo yum install php-devel php-pear httpd-devel
   sudo su -c 'yum -y install zlib-devel cyrus-sasl-devel'
   cd /tmp
   wget https://launchpad.net/libmemcached/1.0/1.0.18/+download/libmemcached-1.0.18.tar.gz
   tar xvf libmemcached-1.0.18.tar.gz
   cd libmemcached-1.0.18
   ./configure
   make
   sudo su -c 'make install'
   sudo su -c 'pecl install memcached'
   sudo su -c 'echo "extension=memcached.so" > /opt/rh/php54/root/etc/php.d/memcached.ini'
   sudo su -c 'echo "memcached.use_sasl=1" >> /opt/rh/php54/root/etc/php.d/memcached.ini'

ffmpeg
======

https://trac.ffmpeg.org/wiki/CompilationGuide/Centos

依赖
----

.. code-block:: sh

   sudo su -c 'yum install autoconf automake gcc gcc-c++ git libtool make nasm pkgconfig zlib-devel yasm freetype-devel'
   mkdir -p ~/.local/bin
   bmemcached-1.0.18mkdir -p ~/Downloads/ffmpeg_build
   mkdir -p ~/Downloads/ffmpeg_sources
   cd ~/Downloads/ffmpeg_sources

libx264
-------

.. code-block:: sh

   git clone --depth 1 git://git.videolan.org/x264
   cd x264
   ./configure --prefix="$HOME/Downloads/ffmpeg_build" --bindir="$HOME/.local/bin" --enable-static
   make
   make install
   make distclean

libfdk_aac
----------

.. code-block:: sh

    git clone --depth 1 git://git.code.sf.net/p/opencore-amr/fdk-aac
    cd fdk-aac
    autoreconf -fiv
    ./configure --prefix="$HOME/Downloads/ffmpeg_build" --disable-shared
    make
    make install
    make distclean

libmp3blame
-----------

.. code-block:: sh

   curl -L -O http://downloads.sourceforge.net/project/lame/lame/3.99/lame-3.99.5.tar.gz
   tar xzvf lame-3.99.5.tar.gz
   cd lame-3.99.5
   ./configure --prefix="$HOME/Downloads/ffmpeg_build" --bindir="$HOME/.local/bin" --disable-shared --enable-nasm
   make
   make install
   make distclean

libopus
-------

.. code-block:: sh

   curl -O http://downloads.xiph.org/releases/opus/opus-1.1.tar.gz
   tar xzvf opus-1.1.tar.gz
   cd opus-1.1
   ./configure --prefix="$HOME/Downloads/ffmpeg_build" --disable-shared
   make
   make install
   make distclean

libogg
------

.. code-block:: sh

   curl -O http://downloads.xiph.org/releases/ogg/libogg-1.3.1.tar.gz
   tar xzvf libogg-1.3.1.tar.gz
   cd libogg-1.3.1
   ./configure --prefix="$HOME/Downloads/ffmpeg_build" --disable-shared
   make
   make install
   make distclean

libvorbis
---------

.. code-block:: sh

   curl -O http://downloads.xiph.org/releases/vorbis/libvorbis-1.3.4.tar.gz
   tar xzvf libvorbis-1.3.4.tar.gz
   cd libvorbis-1.3.4
   ./configure --prefix="$HOME/Downloads/ffmpeg_build" --with-ogg="$HOME/Downloads/ffmpeg_build" --disable-shared
   make
   make install
   make distclean

libvpx
------

.. code-block:: sh

   # git clone --depth 1 https://chromium.googlesource.com/webm/libvpx.git
   wget http://anduin.linuxfromscratch.org/sources/other/libvpx-v1.3.0.tar.xz
   cd libvpx
   ./configure --prefix="$HOME/Downloads/ffmpeg_build" --disable-examples
   make
   make install
   make clean

FFmpeg
------

.. code-block:: sh

    git clone --depth 1 git://source.ffmpeg.org/ffmpeg
    cd ffmpeg
    PKG_CONFIG_PATH="$HOME/Downloads/ffmpeg_build/lib/pkgconfig"
    export PKG_CONFIG_PATH
    ./configure --prefix="$HOME/Downloads/ffmpeg_build" --extra-cflags="-I$HOME/Downloads/ffmpeg_build/include" --extra-ldflags="-L$HOME/Downloads/ffmpeg_build/lib" --bindir="$HOME/.local/bin" --extra-libs=-ldl --enable-gpl --enable-nonfree --enable-libfdk_aac --enable-libmp3lame --enable-libopus --enable-libvorbis --enable-libvpx --enable-libx264 --enable-libfreetype
    make
    make install
    make distclean
    hash -r
    . ~/.bash_profile

激活 php54 和 mysql55
=====================

.. code-block:: sh

   echo "source /opt/rh/php54/enable
   source /opt/rh/mysql55/enable
   " >> ~/.bashrc
   source ~/.bashrc

服务管理
========

启动服务
--------

.. code-block:: sh

   service nginx start
   service php54-php-fpm start
   service mysql55-mysqld start
