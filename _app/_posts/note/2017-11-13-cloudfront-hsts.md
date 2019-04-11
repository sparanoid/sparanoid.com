---
layout: post
title: HSTS for Amazon CloudFront
category: note
---

As we know Amazon CloudFront doesn't support HSTS (HTTP Strict Transport Security) headers. If you tried adding it via CLI or web console, it will be prefixed with `x-amz-`.

But today we're now able to use AWS [Lambda@Edge](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-at-the-edge.html) to execute functions that customize the content that is delivered through CloudFront.

You can access AWS Lambda via its web console, then create (author) a function from scratch, create a new role from template Basic Edge Lambda permissions, then use the following code:

```js
'use strict';
exports.handler = (event, context, callback) => {
  const response = event.Records[0].cf.response;
  const headers = response.headers;

  headers['strict-transport-security'] = [{
    key: 'Strict-Transport-Security',
    value: 'max-age=31536000; includeSubdomains; preload'
  }];

  callback(null, response);
};
```

The above code snippet is straightforward: simply add the `Strict-Transport-Security` we need and return the modified header to edge nodes. Then you can publish a new version, create a CloudFront trigger using Distribution ID for the distribution you want to apply this header. Make sure to select `Origin Response` for your Lambda function to listen for.

Once this is submitted new headers will take effect as expected:

```shell
$ curl -I https://sparanoid.com/
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 6816
Connection: keep-alive
Strict-Transport-Security: max-age=31536000; includeSubdomains; preload
Age: 9128
Cache-Control: public, max-age=1800, immutable
...
```

Updated Mar 21, 2018: Now Amazon Web Services has an official documentation for this feature: [Adding HTTP Security Headers Using Lambda@Edge and Amazon CloudFront
](https://aws.amazon.com/blogs/networking-and-content-delivery/adding-http-security-headers-using-lambdaedge-and-amazon-cloudfront/).
