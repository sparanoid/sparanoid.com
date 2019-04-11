---
layout: post
title: Public Key Pinning / HPKP Reporting
category: note
---

(HTTP) Public Key Pinning (HPKP) allows websites to send an HTTP header instructing the browser to remember (or "pin") parts of its SSL certificate chain. The browser will then refuse subsequent connections that don’t match the pins that it has previously received.

As of September of 2015, both Google Chrome and Mozilla Firefox of any modern platforms support HTTP Public Key Pinning [^1]. And Google Chrome supports HPKP reporting from the version 46 [^2].

## Creating Pin Hashes

Things you should know:

- The most secure public key to pin is your own site certificate key. But you should really take care of revoking or replacing certificate before the `max-age` you defined expires.
- A simpler but less secure way is to use the first intermediate certificate from issued CA.
- You must have two `pin-sha256` hashes according to the RFC. The first one of the hashes should be valid and matched your current certificate, the second should be the hash of your backup certificate which **should not** be in your current certificate chain. That means even a fake pin would work in this scenario.

Let's pin site certificate key for better security:

```shell
$ openssl rsa -in my.key -outform der -pubout | openssl dgst -sha256 -binary | openssl enc -base64
```

If signed via ECDSA (i.e. COMODO ECC):

```shell
$ openssl ec -in my.key -outform der -pubout | openssl dgst -sha256 -binary | openssl enc -base64
```

## Adding Header for Nginx

Once you got the pins add the following line to Nginx:

```nginx
add_header Public-Key-Pins 'pin-sha256="ABCD"; pin-sha256="EFGI"; max-age=86400';
```

## Test HPKP

If everything is right, you can get the result from `chrome://net-internals/#hsts` in Google Chrome:

```yaml
dynamic_sts_domain: ci.sparanoid.com
dynamic_upgrade_mode: STRICT
dynamic_sts_include_subdomains: true
dynamic_sts_observed: 1443522549.33342
dynamic_pkp_domain: ci.sparanoid.com
dynamic_pkp_include_subdomains: true
dynamic_pkp_observed: 1443522549.333427
dynamic_spki_hashes: sha256/6X0iNAQtPIjXKEVcqZBwyMcRwq1yW60549axatu3oDE=,sha256/klO23nT2ehFDXCfx3eHTDRESMz3asj1muO+4aIdjiuY=
```

You can also see if Public Key Pinning is enabled from Web Inspector in Mozilla Firefox.

## HPKP Reporting

At the time of writing, Google Chrome already supports HPKP reporting, so can test it with a report-only header and a fake primary pin, I recommend [Report URI](https://report-uri.com/) or [RequestBin](https://requestbin.com/) for collecting requests:

```nginx
add_header Public-Key-Pins-Report-Only 'pin-sha256="FAKE"; pin-sha256="EFGI"; max-age=86400; report-uri="https://get.report-uri.com/r/d/hpkp/reportOnly"';
```

## `max-age` Best Practice

According to RFC:

> As mentioned in Section 2.3.3, UAs MAY cap the max-age value at some upper limit.  There is a security trade-off in that low maximum values provide a narrow window of protection for users who visit the Known Pinned Host only infrequently, while high maximum values might result in a UA's inability to successfully perform Pin Validation for a Known Pinned Host if the UA's noted Pins and the host's true Pins diverge.

[^1]: See "Browser compatibility" section -- [Public Key Pinning - MDN](https://developer.mozilla.org/en-US/docs/Web/Security/Public_Key_Pinning)

[^2]: HPKP reporting, shipping in Chrome 46, is a feature that you can use to detect misconfigurations as you’re rolling out HPKP. -- [Rolling out Public Key Pinning with HPKP Reporting — Google Web Updates](https://developers.google.com/web/updates/2015/09/HPKP-reporting-with-chrome-46)
