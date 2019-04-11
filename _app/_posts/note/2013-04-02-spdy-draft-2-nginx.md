---
layout: post
title: SPDY Draft 2 and Nginx
category: note
---

I've already [deployed SPDY](/note/playing-with-spdy-draft-2/) back in September, 2012. Now I'd like to share how to setup SPDY and OpenSSL on CentOS with you.

SPDY has a dependency with OpenSSL, and you also need the latest OpenSSL, the latest version is 1.0.1e as I wrote this post.

First install OpenSSL 1.0.1 from IUS repo for Nginx:

```sh
$ yum replace openssl --replace-with openssl10 --enablerepo ius-testing
```

The current stable version of Nginx still doesn't support SPDY, you need to compile it from source, the latest Nginx version that supports SPDY is 1.3.14 with SPDY patch.

**Aprl 25, 2013 update**: SPDY patch has been merged into Nginx 1.4.0 and 1.3.15, so you don't need to compile it any more, but you still need OpenSSL 1.0.1 or higher to make SPDY work.


Compile Nginx with SPDY support:

```sh
$ cd /tmp/
$ wget https://nginx.org/download/nginx-1.3.14.tar.gz
$ tar zxvf nginx-*.tar.gz
$ cd nginx-*
```

Install Nginx dependencies:

```sh
$ yum install -y patch gcc pcre-devel zlib-devel
```

Patch Nginx with SPDY support:

```sh
$ wget https://nginx.org/patches/spdy/patch.spdy.txt
$ patch -p1 < patch.spdy.txt
```

Compile Nginx with OpenSSL:

```sh
$ cd ..
$ wget https://www.openssl.org/source/openssl-1.0.1e.tar.gz
$ tar zxvf openssl-*.tar.gz
$ cd nginx-*
```

Configure Nginx with options `--with-http_spdy_module` and `--with-openssl=/tmp/openssl-1.0.1e`:

```sh
$ ./configure --prefix=/etc/nginx/ --sbin-path=/usr/sbin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_stub_status_module --with-mail --with-mail_ssl_module --with-file-aio --with-ipv6 --with-cc-opt='-O2 -g -march=i386 -mtune=i686' --with-http_spdy_module --with-openssl=/tmp/openssl-1.0.1e
$ make
$ make install
```

Now Nginx is configured and compiled with SPDY support, then you need to enable SPDY for your server directives:

```nginx
server {
  listen               80       default_server;
  listen               [::]:80  default_server ipv6only=on;

  listen               443      ssl spdy default_server;
  listen               [::]:443 ssl spdy default_server ipv6only=on;
}
```

Restart (reload) your Nginx, open chrome://net-internals/#spdy in your Google Chrome to see if SPDY is enabled correctly. You can also try [SPDY indicator](https://chrome.google.com/webstore/detail/spdy-indicator/mpbpobfflnpcgagjijhmgnchggcjblin?hl=en) extension.
