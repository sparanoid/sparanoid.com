---
layout: post
title: CloudFront Cost Control
category: note
---

If you're using CloudFront to serve your website, here's some tips that can help reduce the cost.

Here's a output sample of my deploy script:

```sh
$ make
[info] Deploying /Sites/sparanoid.com/* to sparanoid.com
[succ] Updated lab/amsf/assets/css/app.css
[succ] Updated lab/amsf/assets/js/custom.js
[succ] Updated lab/amsf/assets/themes/curtana/js/intense.js
[succ] Invalidated 3 items on CloudFront
[info] Summary: Updated 3 files. Transferred 15.2 kB, 8.0 kB/s.
```

The invalidation price will be high if you like me, deploy sites via git hooks so every time you deploy sites to CloudFront the invalidation triggers. So here's a workaround way to avoid invalidation but also refresh caches for users.

> File hashes to the rescue.

Here's another output sample of optimized deploy script:

```sh
$ make
[info] Deploying /Sites/sparanoid.com/* to sparanoid.com
[succ] Deleted lab/amsf/assets/css/app.css
[succ] Deleted lab/amsf/assets/js/custom.js
[succ] Deleted lab/amsf/assets/themes/curtana/js/intense.js
[succ] Created lab/amsf/assets/css/app.17b3a8ad.css
[succ] Created lab/amsf/assets/js/custom.53609257.js
[succ] Created lab/amsf/assets/themes/curtana/js/intense.5797aa35.js
[info] Summary: Created 3 files. Deleted 3 files. Transferred 15.2 kB, 8.2 kB/s.
```

There's no invalidation anymore, The cost of invalidation requests is first 1,000 paths free, then $0.005 per path. For example if you deploy your sites for client preview, let's say 20 there're 10 deploys per day with an average of 20 files (CSS, scripts and images) per deploy, all done automatically via git hooks. You will have 200 invalidations per day. 1000 for five days then you'll run out of free tier. you have to pay $5 per 1000 request if you continue requesting invalidations.

So in my [current setup](https://sparanoid.com/lab/amsf/), I use [grunt-cache-bust](https://github.com/hollandben/grunt-cache-bust) to hash files. All file references will be updated automatically via Grunt task.

### Update Jun 19, 2016

With recent update of AWS CloudFront, you can now use the `*` wildcard to invalidate files. In my case, I can just invalidate the whole distribution on the root with only one invalidation request:

```json
{
    "Invalidation": {
        "Status": "Completed",
        "InvalidationBatch": {
            "Paths": {
                "Items": [
                    "/*"
                ],
                "Quantity": 1
            },
            "CallerReference": "s3_website gem 1466341232813"
        },
        "Id": "I3CS10X18L9R4R",
        "CreateTime": "2016-06-19T13:00:32.999Z"
    }
}
```

Compared to:

```json
{
    "Invalidation": {
        "Status": "Completed",
        "InvalidationBatch": {
            "Paths": {
                "Items": [
                    "/lab/amsf/feed.json",
                    "/lab/amsf/feed.xml",
                    "/lab/amsf/404.html",
                    "/lab/amsf/markup-example.html",
                    "/lab/amsf/creating-themes.html",
                    "/lab/amsf/getting-started.html",
                    "/lab/amsf/custom-css.html",
                    "/lab/amsf/external-link-post.html",
                    "/lab/amsf/github-pages-setup.html",
                    "/lab/amsf/markdown-features-test.html",
                    "/lab/amsf/theme-curtana.html",
                    "/lab/amsf/multiple-themes-support.html",
                    "/lab/amsf/themes.html",
                    "/lab/amsf/syntax-highlighting.html",
                    "/lab/amsf/news/",
                    "/lab/amsf/configuration.html",
                    "/lab/amsf/custom-html-markups.html",
                    "/lab/amsf/svg-post-title.html",
                    "/lab/amsf/sitemap.xml",
                    "/lab/amsf/welcome.html",
                    "/lab/amsf/robots.txt",
                    "/lab/amsf/about/",
                    "/lab/amsf/deployment-methods.html",
                    "/lab/amsf/",
                    "/lab/amsf/customizing-styles.html",
                    "/lab/amsf/custom-color-scheme.html",
                    "/index.html"
                ],
                "Quantity": 27
            },
            "CallerReference": "s3_website gem 1466264746013"
        },
        "Id": "I3EAPBBE5NHVC6",
        "CreateTime": "2016-06-18T15:45:46.216Z"
    }
}
```

1 vs 27, and according to the [AWS docs](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html):

> The first 1,000 invalidation paths that you submit per month are free; you pay for each invalidation path over 1,000 in a month. An invalidation path can be for a single object (such as `/images/logo.jpg`) or for multiple objects (such as `/images/*`). A path that includes the `*` wildcard counts as one path even if it causes CloudFront to invalidate thousands of objects.

So this update could dramatically reduce your invalidation cost.
