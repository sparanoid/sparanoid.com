---
layout: post
title: OCSP Stapling and Nginx
category: note
---

I've already [deployed OCSP Stapling](/note/playing-with-ocsp-stapling/) back in November, 2012. Now I'd like to share how to setup OCSP Stapling on CentOS with you.

Configurate OCSP for sparanoid.com. Get your current SSL certificates chain order:

```sh
$ openssl s_client -showcerts -connect sparanoid:443  ("level" c ".crt")} /---END CERTIFICATE-----/{inc=0}'
```

Print out the OCSP server:

```sh
$ openssl x509 -noout -text -in sparanoid.com.crt | grep OCSP
```

Validate certs against the OCSP server:

```sh
$ openssl ocsp -text -no_nonce -issuer PositiveSSLCA2.crt -CAfile sparanoid_com.crt -cert sparanoid_com.crt -VAfile PositiveSSLCA2.crt -url http://ocsp.comodoca.com -respout /etc/ssl/certs/sparanoid.com_staple
```

Add OCSP Stapling support for Nginx, for more infomation you should refer to [Nginx's `ngx_http_ssl_module`](https://nginx.org/en/docs/http/ngx_http_ssl_module.html):

```nginx
ssl_stapling            on;
resolver                8.8.8.8;
ssl_stapling_file       /etc/ssl/certs/sparanoid.com_staple;
ssl_stapling_verify     on;
```

Reload nginx to validate if OCSP Stapling is working:

```sh
$ openssl s_client -connect sparanoid.com:443 -tls1_2 -tlsextdebug -status
```

### The Problem

Updated Jan 20, 2014, 6:29 PM: So far the above guide is not working any more, so what did I do wrong? Please recheck my configuration:

```nginx
ssl_stapling_file       /etc/ssl/certs/sparanoid.com_staple;
```

The `ssl_stapling_file` configuration itself is not a problem. However, the problem is that the stapling response should be updated periodically. So the above configuration resulted an outdated OCSP response:

```
OCSP Response Data:
    OCSP Response Status: successful (0x0)
    ...
    Produced At: Apr 12 12:20:35 2013 GMT
    ...
    Cert Status: good
    This Update: Apr 12 12:20:35 2013 GMT
    Next Update: Apr 16 20:20:35 2013 GMT
```

### The Solution

So how to solve this issue? You don't need cron to fetch the stapling file, for example you can use the following configuration:

```nginx
ssl_stapling            on;
ssl_stapling_verify     on;
resolver                8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout        3s;
ssl_trusted_certificate /etc/nginx/ssl/ca/PositiveSSL-CA.bundle.crt;
```

Instead of defining `ssl_stapling_file`, use `ssl_trusted_certificate` will let Nginx update OCSP response automatically, so it's recommended to define a failover DNS resolver and a small resolver timeout.

## Notes

The `ssl_trusted_certificate` option **must** point to the root certificate and all intermediate certificates of the CA:

> For verification to work, the certificate of the server certificate issuer, the root certificate, and all intermediate certificates should be configured as trusted using the `ssl_trusted_certificate` directive.

You can also enable OCSP Stapling for multiple server directives. However OCSP Stapling must be first enabled in the `default_server` directive before it can be enabled on any other directive.
